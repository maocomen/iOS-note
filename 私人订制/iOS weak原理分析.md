```objective-c
// Update a weak variable.
// If HaveOld is true, the variable has an existing value 
//   that needs to be cleaned up. This value might be nil.
// If HaveNew is true, there is a new value that needs to be 
//   assigned into the variable. This value might be nil.
// If CrashIfDeallocating is true, the process is halted if newObj is 
//   deallocating or newObj's class does not support weak references. 
//   If CrashIfDeallocating is false, nil is stored instead.
template <bool HaveOld, bool HaveNew, bool CrashIfDeallocating>
static id 
storeWeak(id *location, objc_object *newObj)//（weak 变量的地址，要指向的对象）
{
    assert(HaveOld  ||  HaveNew);//只要HaveOld 和 HaveNew 其中一个不是Yes，就退出
    if (!HaveNew) assert(newObj == nil);//如果 HaveNew 为 No，且 newObj 不是 nil，退出

    Class previouslyInitializedClass = nil;
    id oldObj;
    SideTable *oldTable;
    SideTable *newTable;

    // Acquire locks for old and new values.
    // Order by lock address to prevent lock ordering problems. 
    // Retry if the old value changes underneath us.
 retry:
    if (HaveOld) {//如果 HaveOld 为 Yes，即有旧值
        oldObj = *location;//将指针指向的对象赋给 oldObj
        oldTable = &SideTables()[oldObj];//取出 SideTables 中对应的 weak 表（这个对象可能其他地方有弱引用）
    } else {
        oldTable = nil;
    }
    if (HaveNew) {//如果 HaveNew 为 Yes，说明要赋新值给它
        newTable = &SideTables()[newObj];//取出新对象对应的 weak 表（这个对象可能有其他地方有弱引用）
    } else {
        newTable = nil;
    }

    SideTable::lockTwo<HaveOld, HaveNew>(oldTable, newTable);//上锁

    if (HaveOld  &&  *location != oldObj) {//如果有旧值，且指针指向的对象不等于旧值 如果 oldObj 与 *location 不同的话，说明 location 可能被其他县城修改了
        SideTable::unlockTwo<HaveOld, HaveNew>(oldTable, newTable);//解锁，返回 retry 地方重新执行
        goto retry;
    }

    // Prevent a deadlock between the weak reference machinery
    // and the +initialize machinery by ensuring that no 
    // weakly-referenced object has an un-+initialized isa.
    if (HaveNew  &&  newObj) {//如果需要赋新值
        Class cls = newObj->getIsa();//取出新值对应的类
        if (cls != previouslyInitializedClass  &&  
            !((objc_class *)cls)->isInitialized()) //判断 isa 是否非空而且已经初始化过
        {
            SideTable::unlockTwo<HaveOld, HaveNew>(oldTable, newTable);//解锁
            _class_initialize(_class_getNonMetaClass(cls, (id)newObj));//执行初始化方法

            // If this class is finished with +initialize then we're good.
            // If this class is still running +initialize on this thread 
            // (i.e. +initialize called storeWeak on an instance of itself)
            // then we may proceed but it will appear initializing and 
            // not yet initialized to the check above.
            // Instead set previouslyInitializedClass to recognize it on retry.
            // 如果该类已经执行完了 +initialize 方法，是最理想情况
            // 如果该类 +initialize 在线程中
            // 例如 +initialize 正在调用 storeweak 方法 ？？？
            // 需要手动对其增加保护策略，并设置 previouslyInitializedClass 指针进行标记
            previouslyInitializedClass = cls;

            goto retry;// 重新尝试
        }
    }

    // Clean up old value, if any.
    if (HaveOld) {
        weak_unregister_no_lock(&oldTable->weak_table, oldObj, location);
    }

    // Assign new value, if any.
    if (HaveNew) {
        newObj = (objc_object *)weak_register_no_lock(&newTable->weak_table, 
                                                      (id)newObj, location, 
                                                      CrashIfDeallocating);
        // weak_register_no_lock returns nil if weak store should be rejected

        // Set is-weakly-referenced bit in refcount table.
        if (newObj  &&  !newObj->isTaggedPointer()) {
            newObj->setWeaklyReferenced_nolock();
        }

        // Do not set *location anywhere else. That would introduce a race.
        *location = (id)newObj;
    }
    else {
        // No new value. The storage is not changed.
    }
    
    SideTable::unlockTwo<HaveOld, HaveNew>(oldTable, newTable);

    return (id)newObj;
}

```

