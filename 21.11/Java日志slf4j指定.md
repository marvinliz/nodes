当应用里出现多个slf4j时，比如log4j和log-back，应用启动时会选择第一个被类加载器加载的。

如果你想指定某个slf4j，但是又不想exclude相应的jar，可以在应用代码内增加以下代码，以下代码来自SkayWalking。

其实slf4j的门面是会加载这个类，必须包名类名一致，但是例子中包名增加了`org.apache.skywalking.apm.dependencies`。因为打包插件中配置了maven-shade-plugin，把/netty/grpc/kafka 包前缀加上了`org.apache.skywalking.apm.dependencies`从而达到了 netty/grpc/kafka 程序使用的是skywalking的log。

当然你不用怎么麻烦，比如你想用log-back，就把logback的org.slf4j.impl.StaticLoggerBinder 复制到你应用内，这样启动时类加载器会优先加载应用类，从而达到了指定slf4j的目的。

slf4j采用这种方式来实现门面模式，感觉没有SPI来的方便，至于为什么会采用这种，只能问作者了。

```java
/**
 * The slf4j-api would try to load org.slf4j.impl.StaticLoggerBinder internal. In the agent core, we add our own implementation
 * for bridging to SkyWalking internal log component. 
 * Therefore, logs of netty/grpc/kafka(agent shaded components) would output through the SkyWalking's log.
 *
 * Don't move this class to any other package, its package must be as same as the shaded org.apache.skywalking.apm.dependencies.org.slf4j.impl 
 */
public final class StaticLoggerBinder implements LoggerFactoryBinder {

    private static final String LOGGER_FACTORY_CLASS_STR = InternalLoggerFactory.class.getName();

    /**
     * The unique instance of this class.
     */
    private static final StaticLoggerBinder SINGLETON = new StaticLoggerBinder();

    /**
     * The ILoggerFactory instance returned by the {@link #getLoggerFactory} method should always be the same object
     */
    private final ILoggerFactory loggerFactory;

    /**
     * Private constructor to prevent instantiation
     */
    private StaticLoggerBinder() {
        loggerFactory = new InternalLoggerFactory();
    }

    /**
     * Returns the singleton of this class.
     * Dot't delete this method, this method will called by {@link org.slf4j.LoggerFactory#bind()}
     *
     * @return the StaticLoggerBinder singleton
     */
    public static StaticLoggerBinder getSingleton() {
        return SINGLETON;
    }

    /**
     * Returns the factory.
     *
     * @return the factor.
     */
    @Override
    public ILoggerFactory getLoggerFactory() {
        return loggerFactory;
    }

    /**
     * Returns the class name.
     *
     * @return the class name;
     */
    @Override
    public String getLoggerFactoryClassStr() {
        return LOGGER_FACTORY_CLASS_STR;
    }
```

