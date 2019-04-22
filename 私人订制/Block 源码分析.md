## _Block_copy

```swift

// Values for Block_layout->flags to describe block objects
enum {
    BLOCK_DEALLOCATING =      (0x0001),  // runtime
    BLOCK_REFCOUNT_MASK =     (0xfffe),  // runtime
    BLOCK_NEEDS_FREE =        (1 << 24), // runtime
    BLOCK_HAS_COPY_DISPOSE =  (1 << 25), // compiler
    BLOCK_HAS_CTOR =          (1 << 26), // compiler: helpers have C++ code
    BLOCK_IS_GC =             (1 << 27), // runtime
    BLOCK_IS_GLOBAL =         (1 << 28), // compiler
    BLOCK_USE_STRET =         (1 << 29), // compiler: undefined if !BLOCK_HAS_SIGNATURE
    BLOCK_HAS_SIGNATURE  =    (1 << 30), // compiler
    BLOCK_HAS_EXTENDED_LAYOUT=(1 << 31)  // compiler
};

#define BLOCK_DESCRIPTOR_1 1
struct Block_descriptor_1 {
    uintptr_t reserved;
    uintptr_t size;
};

#define BLOCK_DESCRIPTOR_2 1
struct Block_descriptor_2 {
    // requires BLOCK_HAS_COPY_DISPOSE
    void (*copy)(void *dst, const void *src);
    void (*dispose)(const void *);
};

struct Block_layout {
    void *isa;
    volatile int32_t flags; // contains ref count
    int32_t reserved; 
    void (*invoke)(void *, ...);
    struct Block_descriptor_1 *descriptor;
    // imported variables
};

static int32_t latching_incr_int(volatile int32_t *where) {
    while (1) {//搞不大懂这里的这个 while 循环的作用
        int32_t old_value = *where;
        //因为malloc Block的 flag 的初始值后16位是 0000 0000 0000 0010，如果是 +2 的话，最大值就是 1111 1111 1111 1110，也就是 fffe。
        if ((old_value & BLOCK_REFCOUNT_MASK) == BLOCK_REFCOUNT_MASK) {
            return BLOCK_REFCOUNT_MASK;
        }
        if (OSAtomicCompareAndSwapInt(old_value, old_value+2, where)) {//如果 where 的值和 old_value 相等，那么将 old_value+2 赋给 where。
            return old_value+2;//这里这个返回值并没有什么鸟用
        }
    }
}

static struct Block_descriptor_2 * _Block_descriptor_2(struct Block_layout *aBlock)
{
    if (! (aBlock->flags & BLOCK_HAS_COPY_DISPOSE)) return NULL;
    uint8_t *desc = (uint8_t *)aBlock->descriptor;//声明一个 unit8_t 指针
    desc += sizeof(struct Block_descriptor_1);//指针向后移动 Block_descriptor_1 内容个字节
    return (struct Block_descriptor_2 *)desc;
}

static void _Block_call_copy_helper(void *result, struct Block_layout *aBlock)
{
    struct Block_descriptor_2 *desc = _Block_descriptor_2(aBlock);//声明一个 Block_descriptor_2 结构体指针
    if (!desc) return;

    (*desc->copy)(result, aBlock); // do fixup
}

// Copy, or bump refcount, of a block.  If really copying, call the copy helper if present.
void *_Block_copy(const void *arg) {
    struct Block_layout *aBlock;

    if (!arg) return NULL;
    
    // The following would be better done as a switch statement
    aBlock = (struct Block_layout *)arg;
    if (aBlock->flags & BLOCK_NEEDS_FREE) {//判断是否是 mallocBlock
        // latches on high
        latching_incr_int(&aBlock->flags);//flag+2 但是为什么是+2不是+1有点搞不懂
        return aBlock;
    }
    else if (aBlock->flags & BLOCK_IS_GLOBAL) {//如果是 global block，直接返回，retain，release，copy 操作对 global block 无效。
        return aBlock;
    }
    else {
        // Its a stack block.  Make a copy.
        struct Block_layout *result = malloc(aBlock->descriptor->size);//Block_layout结构体中的 descriptor 指针指向一个 BLOCK_DESCRIPTOR_1 结构体，BLOCK_DESCRIPTOR_1 结构体中的 size 是当前 block 占用多少个字节。这里申请系统分配一块儿原 block 大小的内存。
        if (!result) return NULL;
        memmove(result, aBlock, aBlock->descriptor->size); // bitcopy first 将原 block 中的内容拷贝到新的 block 中去
        // reset refcount
        result->flags &= ~(BLOCK_REFCOUNT_MASK|BLOCK_DEALLOCATING);    // XXX not needed 消除 flags 的低 16 位，使他们变成 0
        result->flags |= BLOCK_NEEDS_FREE | 2;  // logical refcount 1 使 flags 第 2 位变成 1，第 25 位变成 1。
        _Block_call_copy_helper(result, aBlock);
        // Set isa last so memory analysis tools see a fully-initialized object.
        result->isa = _NSConcreteMallocBlock;//修改 isa 指针指向，在demo中测试感觉这个指针并没有修改成功？_NSConcreteMallocBlock 是个指针指针数组并不是类？
        return result;
    }
}

```

## _Block_release

```swift
// API entry point to release a copied Block
void _Block_release(const void *arg) {
    struct Block_layout *aBlock = (struct Block_layout *)arg;//将 block 转化为 Block_layout 指针
    if (!aBlock) return;//如果 block 是空，return
    if (aBlock->flags & BLOCK_IS_GLOBAL) return;//如果是 global 类型的 block，直接 return。这也说明 release，retain，copy 对 global block 无效。
    if (! (aBlock->flags & BLOCK_NEEDS_FREE)) return;

    if (latching_decr_int_should_deallocate(&aBlock->flags)) {
        _Block_call_dispose_helper(aBlock);
        _Block_destructInstance(aBlock);
        free(aBlock);
    }
}
```

