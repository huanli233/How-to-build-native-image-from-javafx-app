# How-to-build-native-image-from-javafx-app
讲述如何构建一个javafx app的native-image（示例Windows）

## 构建可直接执行的 JAR 文件
正常情况下启动 JavaFX 程序需要添加 VM 参数，比如：
```
java --module-path javafx-sdk\lib --add-modules javafx.fxml,javafx.controls -jar app.jar
```
如何构建一个可以直接 “**双击打开**” 的 JAR 包？
*****
1. 如果程序的主类继承 *Application* ，请将主类设置为另一个“*Launcher*”类并在main方法中使用
   ```java
   Application.launch(MyApp.class,args);
   ```
2. 如果你直接导出 JAR（不使用构建工具），请将javafx-sdk下bin的依赖一起打包进 JAR 。
   
   如果你使用 maven ，你可以用 shade 打包。
3. 然后直接运行 JAR 试试看能不能直接运行。
（我没有试过非模块化的项目）

## 构建 native-image
**环境需求：**
**maven版本不能太高，若构建过程中提示maven版本有问题请降级（我用的3.8.5）**
**GraalVM版本：graalvm-ee-java17-22.3.3**
1. 使用Graalvm安装目录\bin下的gu.cmd执行命令（若你配置了环境变量则直接输入命令即可）
   ```
   gu install native-image
   ```
2. 然后在你程序的 JAR 目录执行命令
   ```
   java -agentlib:native-image-agent=config-output-dir=native-image -jar your-program.jar
   ```
   然后你最好多用程序执行一下各种功能，然后退出。
3. 将刚刚生成的native-image文件夹复制到你项目的resource/META-INF中。
4. 然后按照仓库里的pom.xml配置GlounFX。
5. 最后执行mvn glounfx:build glounfx:nativerun。
  
   然后在target\glounfx中查看生成的本机映像（如果构建成功）
