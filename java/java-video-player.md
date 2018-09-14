---
title: 简单的Java视频播放器
---

# 简单的Java视频播放器

从Java 7开始，JavaFX就被集成到了Java的标准库中，因此可以在Java应用中直接使用，无需安装额外的库。JavaFX有一套功能丰富的视频处理工具，包含在包`javafx.scene.media`中，这使得写Java视频播放器变得非常简单。

## 开始写一个简单的JavaFX应用

要写一个简单的JavaFX应用，只需让Main类继承`javafx.application.Application`，实现所需的`start`函数，并在`main`函数中启动应用。最简单的JavaFX程序框架如下

```java
public class Main extends Application {
    public stati void main(String[] args) {
        Application.launch(args);
    }
    
    @Override
    public void start(Stage stage) throws Exception {
        
    }
}
```

其中，`start`函数负责初始化程序的图形化界面场景。首先创建一个场景`javafx.scene.Scene`，然后设置它为stage的场景，最后显示stage。

```java
Scene scene = new Scene(new Group(), width, height);
stage.setScene(scene);
stage.show();
```

为了向场景中添加控件元素，可以给临时创建的`javafx.scene.Group`对象命名。

```java
Group root = new Group();
Scene scene = new Scene(root, width, height);
```

然后调用`root.getChildren().add()`函数来向场景中添加控件。

## 添加视频播放控件

在简单的视频播放应用中，以下的代码可以都放在`start`函数里。

首先创建视频对象`javafx.scene.media.Media`。

```java
Media media = new Media(uri); 
```

然后创建播放器`javafx.scene.media.MediaPlayer`。

```java
MediaPlayer player = new MediaPlayer(media); 
```

创建视频显示控件`javafx.scene.media.MediaView`。

```java
MediaView view = new MediaView(player); 
```

将视频控件添加到场景中

```java
root.getChildren().add(view);
```

播放

```java
player.play();
```

## 添加功能

让视频大小随窗口变化

```java
view.fitWidthProperty().bind(scene.widthProperty());
```

点击视频暂停或继续播放

```java
view.setOnMouseClicked(e -> {
    if(player.getStatus().equals(MediaPlayer.Status.PLAYING))
        player.pause();
    else
        player.play();
});
```

