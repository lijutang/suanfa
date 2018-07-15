# Golang 设计模式
# 目录

## 1.单列模式
>为了实现Singleton模式，我们有不同的方法，但它们都有以下常见概念。
* 私有构造函数，用于限制其他类的实例化。
* 同一个类的私有静态变量，它是该类的唯一实例。
* 返回类实例的公共静态方法，这是外部世界获取单例类实例的全局访问点。
### 1.1 Eager initialization(饿汉方式)
>在急切的初始化中，Singleton类的实例是在类加载时创建的，这是创建单例类的最简单方法，但它有一个缺点，即即使客户端应用程序可能没有使用它，也会创建实例。

> 缺点：如果singleton创建初始化比较复杂耗时时，加载时间会延长。
#### 实现
```go
type SingletonLazy struct{}

var insSingletonLazy *SingletonLazy

// 获取懒汉模式对象
func GetInstanceSingletonLazy() *SingletonLazy {
	if insSingletonLazy == nil {
		insSingletonLazy = &SingletonLazy{}
	}
	return insSingletonLazy
}
```
#### 测试
```go
t.Run("TesSingletonLazy...", func(t *testing.T) {
    ins1 := GetInstanceSingletonLazy()
    ins2 := GetInstanceSingletonLazy()
    if ins1 != ins2 {
        t.Fatal("instance is not equal")
    }
})
```

### 1.2 Lazy Initialization(懒汉初始化)
>实现Singleton模式的延迟初始化方法在全局访问方法中创建实例。以下是使用此方法创建Singleton类的示例代码。

>缺点：非线程安全。当正在创建时，有线程来访问此时ins = nil就会再创建，单例类就会有多个实例了。
#### 实现
```go
type SingletonEager struct{}

var insSingletonEager *SingletonEager = &SingletonEager{}

// 获取饿汉模式对象
func GetInstanceSingletonEager() *SingletonEager {
	return insSingletonEager
}
```
#### 测试
```go
t.Run("SingletonEager...", func(t *testing.T) {
    ins1 := GetInstanceSingletonEager()
    ins2 := GetInstanceSingletonEager()
    if ins1 != ins2 {
        t.Fatal("instance is not equal")
    }
})
```

### 1.3 Lazy And Lock Initialization(懒汉加锁初始化)
> 缺点：虽然解决并发的问题，但每次加锁是要付出代价的

#### 实现
```go
type SingletonLazyLock struct{}

var insSingletonLazyLock *SingletonLazyLock
var muSingletonLazyLock sync.Mutex

func GetInstanceSingletonLazyLock() *SingletonLazyLock {
	muSingletonLazyLock.Lock()
	defer muSingletonLazyLock.Unlock()
	if insSingletonLazyLock == nil {
		insSingletonLazyLock = &SingletonLazyLock{}
	}
	return insSingletonLazyLock
}
```
#### 测试
```go
t.Run("SingletonEager...", func(t *testing.T) {
    ins1 := GetInstanceSingletonLazyLock()
    ins2 := GetInstanceSingletonLazyLock()
    if ins1 != ins2 {
        t.Fatal("instance is not equal")
    }
})
```