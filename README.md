# Singleton
> 单例的正确写法（ARC环境下）

-------
#### TestSingleton.h

    #import <Foundation/Foundation.h>
    
    @interface TestSingleton : NSObject <NSCopying>
    
    @property (nonatomic , copy) NSString *gid;
    
    @property (nonatomic , strong) NSMutableArray *groups;
    
    + (instancetype)shareInstance;
    
    @end
    
   
   
-------

####    TestSingleton.m
        
    #import "TestSingleton.h"
    
    @implementation TestSingleton
    
    static TestSingleton *instance = nil;
    
    + (instancetype)shareInstance
    {
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^{
            instance = [[super allocWithZone:NULL] init];
            instance.groups = [[NSMutableArray alloc] init];
            instance.gid = @"0";
        });
        return instance;
    }
    
    //拦截外部调用alloc方法创建单例实例
    + (instancetype)allocWithZone:(struct _NSZone *)zone
    {
        return [TestSingleton shareInstance];
    }
    
    //拦截外部调用copy方法拷贝单例实例
    - (id)copyWithZone:(NSZone *)zone
    {
        return [TestSingleton shareInstance];
    }
    
    //测试单例对象和相关属性的打印地址
    - (NSString *)description
    {
        NSString *result = @"";
        result = [result stringByAppendingFormat:@"<%@ : %p>", [self class], self];
        result = [result stringByAppendingFormat:@" gid = %p", self.gid];
        result = [result stringByAppendingFormat:@" groups = %p", self.groups];
        return result;
    }
    
    @end
    
    
    
    
##  关于TestSingleton.m中的这两个方法

#### + (instancetype)allocWithZone:(struct _NSZone *)zone 
#### - (id)copyWithZone:(NSZone *)zone


-------


我们知道，创建对象的步骤分为**申请内存(alloc)**、**初始化(init)**这两个步骤，我们要确保对象的唯一性，因此在第一步这个阶段我们就要拦截它。当我们调用alloc方法时，OC内部会调用**allocWithZone**这个方法来申请内存，我们覆写这个方法，然后在这个方法中调用**shareInstance**方法返回单例对象，这样就可以达到我们的目的。拷贝对象也是同样的原理，覆写**copyWithZone**方法（**要先遵循NSCopying协议，NSObject并未遵循该协议**），然后在这个方法中调用**shareInstance**方法返回单例对象。




