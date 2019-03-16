SOFAArk 提供了简单的事件总线，Plugin 和 Biz 可以使用事件总线服务 `EventAdminService` 注册事件监听器并发布和监听事件：

```java
public interface EventAdminService {

    /**
     * Initiate synchronous delivery of an event. This method does not return to
     * the caller until delivery of the event is completed.
     *
     * @param event The event to send to all listeners which subscribe to the
     *        topic of the event.
     */
    void sendEvent(ArkEvent event);

    /**
     * Register an event handler
     *
     * @param eventHandler
     */
    void register(EventHandler eventHandler);

    /**
     * un-register an event handler
     * @param eventHandler
     */
    void unRegister(EventHandler eventHandler);

    /**
     * un-register event handler whose classLoader matches the specified param.
     * @param classLoader
     */
    void unRegister(ClassLoader classLoader);
}
```

`EventAdminSerivce` 接口定义四个方法，通过这四个方法，Plugin 和 Biz 可以方便地注册事件监听器以及发送事件，目前只支持同步事件。

### 事件
实现统一的事件接口类型：

```java
public interface ArkEvent {
    /**
     * Returns the topic of event
     *
     * @return String return event topic
     */
    String getTopic();
}
```

调用 `EventAdminService.sendEvent` 方法广播事件，通知所有的监听器 `EventHandler`

### 事件监听器
实现统一的事件监听器接口：

```java
public interface EventHandler extends PriorityOrdered {
    /**
     * Called by the {@link EventAdminService} service to notify the listener of an
     * event.
     *
     * @param event The event that occurred.
     */
    void handleEvent(ArkEvent event);
}
```  

调用 `EventAdminService.register` 方法注册监听器，即可接受广播事件。

### 注意事项
Plugin 内部只能手动注册事件监听器，如果 Biz 是 Spring Boot/SOFABoot 应用，所有实现接口 `EventHandler` 的 Bean 都会默认被注册，这也是在 Biz 中推荐的用法，在应用卸载时，也会自动注销监听器
