#### maxMemory
> 能够从整个系统中拿到的最大内存

#### totalMemory
> 应用运行过程中，总是一点一点从系统中拿内存，基本上用多少拿多少，直到拿到maxMemory内存为止，所以totalMemory是慢慢逐渐增大的

#### freeMemory
> 应用所获得的的内存中暂时没有用到的部分，totalMemory一般会比需要的多一点，多出来的就是freeMemory，所以freeMemory一般会很小