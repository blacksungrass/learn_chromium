目标：

在浏览器中点击dom中的一个元素，触发其对应的click监听事件。找出这个过程中的代码路径



尝试方向1：

从浏览器进程初始化开始，试图找到其分发windows消息的地方

```
 //content\browser\browser_main.c BrowerMain
 std::unique_ptr<BrowserMainRunnerImpl> main_runner(
      BrowserMainRunnerImpl::Create());

  int exit_code = main_runner->Initialize(std::move(parameters));
  if (exit_code >= 0)
    return exit_code;

  exit_code = main_runner->Run();

  main_runner->Shutdown();
```

顺着这条路径，还是没找到分发消息的地方。

不过有找到base中的event_loop中有对windows消息泵的包装，其中调用了windows api peekmessage，不过不清楚chromium中是不是使用的这个。



尝试方向2：

从render_widget_host方向来找找看



third_party\blink\renderer\platform\widget\input\widget_input_handler_impl.h

third_party\blink\renderer\core\frame\web_frame_widget_impl.cc HandleMouseDown