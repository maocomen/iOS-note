#### 在32位系统中

int 占4个字节 
long 占4个字节 
NSInteger 是int的别名，占4个字节 
long long 占8个字节 
int32_t 是int的别名，占4个字节 
int64_t 是long long的别名，占8个字节



#### 在64位系统中

int 占4个字节 
long 占8个字节 
NSInteger 是long的别名，占8个字节 
long long 占8个字节 
int32_t 是int的别名，占4个字节 
int64_t 是long long的别名，占8个字节

**由于long和NSInteger的字节数变了，所以在兼容的时候可能会导致溢出**