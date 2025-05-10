# 实验五报告

> 学号：3225706001
> 
> 姓名：靳慧琳
> 
> 指导老师：张凯斌
> 
> 实验日期：<2025-04-10>

## 一、实验目的

- 完成移动APP的原始框架，将作为后续系统的承载容器；
- 掌握教科书中网络编程相关部分练习；

## 二、实验内容

- 阅读教科书的第7章“网络编程”；
- 结合理论课要求完成本次APP框架设计；

## 三、实验要求

- Loading Page：完成起始页展示
  - 展示个性化设计
  - 可采用点击或定时方式完成跳转
- Login/Registration Page：实现登录/注册功能
  - 本次实验只要求完成页面设计
  - 具体功能在后续实验中完成
    - 主要涉及关系数据库操作
- Home Page：主界面，承担APP主要功能展示（以按钮形式）和跳转
  - 本次实验实现三个功能（按钮）
    - 请求图像
      - 要求使用Image组件的ArrayBuffer形式，<font color=red>不能直接使用URL返回</font>
      - Image组件可直接放在Home Page
    - 请求JSON
      - 采用RichText组件展示请求后的数据
      - RichText在新页面，需跳转
      - 理解异步/同步概念，实现页面跳转后的数据正确展示
    - 请求Web
      - 采用Web组件，直接请求本地HTML，展示2019年世界各国农作物产量预测系统
        - HTML文件在实验五Git课程仓库
      - Web组件在新页面，需跳转
      - 初步理解Web应用，为后续WebGIS二次开发打基础
- 撰写并提交实验报告；
  - <font color=red>实验步骤部分需严格按模板要求撰写</font>

## 四、实验步骤

### 1. Loading Page

#### 1.1 截图展示
说明：背景图+logo+文字布局
![alt text](../Loading.png)

#### 1.2 代码实现

```typescript {.line-numbers}
// 导入用户隐私对话框组件
import UserPrivacyDialog from '../dialog/UserPrivacyDialog'
// 从AbilityKit中导入common模块
import { common } from '@kit.AbilityKit'

// 装饰器，表示这个组件可以在预览器中预览
@Preview
// 装饰器，表示这是应用的入口组件
@Entry
// 装饰器，表示这是一个自定义组件
@Component
// 定义名为Index的结构体组件
struct Index {
  // 声明UIAbilityContext类型的context变量，用于获取当前上下文
  context : common.UIAbilityContext = getContext(this) as common.UIAbilityContext;
  
  // 创建自定义对话框控制器实例
  dialogController: CustomDialogController = new CustomDialogController({
    // 使用UserPrivacyDialog作为对话框内容
    builder:UserPrivacyDialog({
      // 这里可以传递参数给UserPrivacyDialog
    })
  })

  // 构建UI的build方法
  build() {
    // 创建一个垂直布局的Column容器
    Column(){
      // 添加图片组件，使用资源管理器$r引用app.media.logo图片
      Image($r('app.media.logo'))
        // 设置图片宽度为100单位
        .width(100)
        // 设置图片上边距为10单位
        .margin({top:10})
      
      // 添加文本组件
      Text('欢迎来到健身app')
        // 设置字体大小为15单位
        .fontSize(15)
        // 设置字体颜色
        .fontColor('#182431')
        // 设置字体粗细为700(加粗)
        .fontWeight(700)
        // 设置字母间距为0.1单位
        .letterSpacing(0.1)
        // 设置透明度为60%
        .opacity(0.6)
        // 设置上下边距
        .margin({top:20,bottom:140})
    }
    // 设置Column高度为100%
    .height('100%')
    // 设置Column宽度为100%
    .width('100%')
    // 设置背景图片，使用资源管理器$r引用app.media.back1图片
    .backgroundImage($r('app.media.back1'))
    // 设置背景图片大小
    .backgroundImageSize({width:'100%',height:'0%'})
    // 设置背景图片位置
    .backgroundImagePosition({x:0,y:-100})
    // 当组件显示时的回调函数
    .onAppear(() => {
      // 设置3秒的定时器
      setTimeout(() => {
        // 3秒后打开对话框
        this.dialogController.open();
      }, 3000); // 3秒后显示弹窗
    })
  }
}
```


### 2. Login/Registration Page

#### 2.1 截图展示
以弹窗形式实现登录注册功能、并将密码设置为不可见
![alt text](../Login.png)
![alt text](../登录功能.png)

#### 2.2 代码实现

```typescript {.line-numbers}
// 导入路由模块
import router from '@ohos.router';

// 装饰器，表示这是一个自定义对话框组件
@CustomDialog
// 导出默认的UserPrivacyDialog结构体组件
export default struct UserPrivacyDialog {
  // 声明并初始化对话框控制器
  controller: CustomDialogController = new CustomDialogController({builder:''});

  // 构建UI的build方法
  build() {
    // 创建一个垂直布局的Column容器，设置子元素间距为10单位
    Column({space: 10}) {
      // 添加标题文本
      Text('欢迎来到健身app')
        .fontSize(30)  // 字体大小30
        .fontWeight(FontWeight.Bolder)  // 字体加粗
        .margin({top:8, bottom:5})  // 上下边距
      
      // 添加副标题文本
      Text('不锻炼好身体怎么做牛马？')
        .fontSize(20)  // 字体大小20
        .fontColor(Color.Pink)  // 粉色字体
      
      // 添加提示文本
      Text('请完成注册')
        .fontSize(15)  // 字体大小15
        .fontColor(Color.Gray)  // 灰色字体
      
      // 添加用户名输入框
      TextInput({
        placeholder: '请输入用户名',  // 占位提示文字
        text: ''  // 初始文本为空
      })
        .onChange((v: string) => {  // 输入内容变化时的回调
          console.log(v);  // 打印输入内容
        })
      
      // 添加密码输入框
      TextInput({
        placeholder: '请输入密码',  // 占位提示文字
        text: ''  // 初始文本为空
      })
        .type(InputType.Password)  // 设置为密码输入类型(显示为圆点)
        .onChange((v: string) => {  // 输入内容变化时的回调
          console.log(v);  // 打印输入内容
        })
      
      // 添加登录按钮
      Button('登录')
        .fontColor(Color.White)  // 白色文字
        .backgroundColor(Color.Black)  // 黑色背景
        .width(150)  // 宽度150单位
        .onClick(() => {  // 点击事件
          this.controller.close();  // 关闭对话框
          router.pushUrl({  // 路由跳转
            url: 'pages/HomePage'  // 跳转到首页
          });
        })
      
      // 添加注册按钮
      Button('注册')
        .fontColor(Color.Gray)  // 灰色文字
        .backgroundColor(Color.Black)  // 黑色背景
        .width(150)  // 宽度150单位
        .onClick(() => {  // 点击事件
          console.log('注册功能尚未实现');  // 打印日志
        })
      
      // 添加忘记密码文本
      Text('忘记密码？')
        .fontSize(10)  // 字体大小10
        .fontColor(Color.Gray)  // 灰色字体
    }
    .width('80%')  // 对话框宽度占屏幕80%
    .height('75%')  // 对话框高度占屏幕75%
  }
}
```

### 3. Home Page

#### 3.1 截图展示
三个Tab页的主页组件，滑动屏幕即可看到三个按钮
![alt text](../HomePageImage.png)
![alt text](../HomePageJson.png)
![alt text](../HomePageWeb.png)

#### 3.2 代码实现

```typescript {.line-numbers}
// 导入网络请求模块
import http from '@ohos.net.http';
// 导入图像处理模块
import image from '@ohos.multimedia.image';
// 导入路由模块
import router from '@ohos.router';

// 入口组件装饰器
@Entry
// 组件装饰器
@Component
// 定义HomePage结构体组件
struct HomePage {
  // 状态变量：当前选中的Tab索引
  @State selectIndex: number = 0;

  // 图像请求相关状态
  @State imagePixelMap: image.PixelMap | null = null;  // 存储图像像素数据
  @State isLoadingImage: boolean = false;  // 图像加载状态
  @State imageProgress: number = 0;  // 图像加载进度

  // JSON请求相关状态
  @State jsonData: string = '';  // 存储JSON数据
  @State isLoadingJson: boolean = false;  // JSON加载状态

  // 创建HTTP请求实例
  private httpRequest: http.HttpRequest = http.createHttp();

  // 请求网络图像方法
  private async requestNetworkImage() {
    this.isLoadingImage = true;  // 开始加载
    this.imageProgress = 0;  // 重置进度

    try {
      // 模拟进度更新的函数
      const updateProgress = () => {
        if (this.imageProgress < 90) {
          this.imageProgress += 10;  // 每次增加10%
          setTimeout(updateProgress, 200);  // 200ms后再次更新
        }
      };
      updateProgress();  // 启动进度更新

      // 发起网络请求获取图像
      const response = await this.httpRequest.request(
        'https://picsum.photos/200/200',  // 随机图片API
        {
          method: http.RequestMethod.GET,  // GET请求
          expectDataType: http.HttpDataType.ARRAY_BUFFER  // 期望返回二进制数据
        }
      );

      // 请求成功处理
      if (response.responseCode === 200) {
        const arrayBuffer = response.result as ArrayBuffer;  // 获取二进制数据
        const imageSource = image.createImageSource(arrayBuffer);  // 创建图像源
        this.imagePixelMap = await imageSource.createPixelMap();  // 生成像素图
        this.imageProgress = 100;  // 进度设为100%
      }
    } catch (error) {
      console.error('网络图像请求失败:', error);  // 错误处理
    } finally {
      this.isLoadingImage = false;  // 结束加载
    }
  }

  // 请求JSON数据并跳转方法
  private async requestJsonAndNavigate() {
    this.isLoadingJson = true;  // 开始加载
    try {
      // 发起JSON请求
      const response = await this.httpRequest.request(
        'https://jsonplaceholder.typicode.com/posts/1',  // 测试API
        {
          method: http.RequestMethod.GET,  // GET请求
          expectDataType: http.HttpDataType.STRING  // 期望返回字符串
        }
      );

      // 请求成功处理
      if (response.responseCode === 200) {
        // 格式化JSON数据
        this.jsonData = JSON.stringify(JSON.parse(response.result as string), null, 2);
        // 跳转到JSON展示页面
        router.pushUrl({
          url: 'pages/JsonPage',
          params: { jsonData: this.jsonData }  // 传递JSON数据
        });
      }
    } catch (error) {
      console.error('JSON请求失败:', error);  // 错误处理
    } finally {
      this.isLoadingJson = false;  // 结束加载
    }
  }

  // 打开农业数据页面方法
  private openAgriculturalData() {
    router.pushUrl({
      url: 'pages/WebPage',
      params: {
        htmlPath: 'resource://rawfile/Agricultural_Crop_Yields_WebApp.html'  // 本地HTML路径
      }
    });
  }

  // Tab栏构建器
  @Builder
  TabBarBuilder(index: number, icon: Resource, text: string) {
    Column() {
      Image(icon)  // Tab图标
        .width(30)
        .height(30)
      Text(text)  // Tab文本
        .fontSize(16)
        .fontColor(this.selectIndex === index ? '#007AFF' : '#999')  // 选中状态颜色变化
    }
    .width('100%')
    .height(50)
    .justifyContent(FlexAlign.Center)  // 居中布局
  }

  // 构建UI
  build() {
    Column() {
      // 根据选中的Tab显示不同内容
      if (this.selectIndex === 0) {
        // 主页内容
        Column({ space: 20 }) {
          Button('加载网络图')  // 加载图片按钮
            .width('90%')
            .height(50)
            .backgroundColor('#007AFF')
            .fontColor('#FFFFFF')
            .onClick(() => this.requestNetworkImage())  // 点击事件
            .margin({ top: 20 })

          // 加载中状态显示
          if (this.isLoadingImage) {
            Column() {
              Progress({ value: this.imageProgress })  // 进度条
                .width(60)
                .height(60)
              Text(`${this.imageProgress}%`)  // 进度百分比
                .fontSize(16)
                .fontColor('#333')
            }
            .width('100%')
            .alignItems(HorizontalAlign.Center)
          }

          // 图片显示区域
          if (this.imagePixelMap) {
            Image(this.imagePixelMap)
              .width(200)
              .height(200)
              .borderRadius(10)  // 圆角
              .objectFit(ImageFit.Contain)  // 保持比例
              .margin({ top: 20 })
          }
        }
        .width('100%')
        .padding(20)
      } else if (this.selectIndex === 1) {
        // 成就页面内容
        Column({ space: 20 }) {
          Button('请求JSON数据并跳转')  // JSON请求按钮
            .width('90%')
            .height(50)
            .backgroundColor('#007AFF')
            .fontColor('#FFFFFF')
            .onClick(() => this.requestJsonAndNavigate())  // 点击事件
            .margin({ top: 20 })
        }
        .width('100%')
        .padding(20)
      } else if (this.selectIndex === 2) {
        // 个人页面内容
        Column({ space: 20 }) {
          Button('打开农业数据')  // 农业数据按钮
            .width('90%')
            .height(50)
            .backgroundColor('#007AFF')
            .fontColor('#FFFFFF')
            .onClick(() => this.openAgriculturalData())  // 点击事件
            .margin({ top: 20 })
        }
        .width('100%')
        .padding(20)
      }

      // 底部Tab栏
      Tabs({ barPosition: BarPosition.End, index: this.selectIndex }) {
        // 首页Tab
        TabContent() {}
        .tabBar(this.TabBarBuilder(0, $r('app.media.Home'), '首页'))

        // 成就Tab
        TabContent() {}
        .tabBar(this.TabBarBuilder(1, $r('app.media.cj'), '成就'))

        // 个人中心Tab
        TabContent() {}
        .tabBar(this.TabBarBuilder(2, $r('app.media.person'), '我的'))
      }
      .onChange((index: number) => {
        this.selectIndex = index;  // Tab切换事件
      })
    }
    .width('100%')
    .height('100%')
  }

  // 组件即将消失时的生命周期方法
  aboutToDisappear() {
    // 释放图像资源
    if (this.imagePixelMap) {
      this.imagePixelMap.release();
    }
    // 销毁HTTP请求
    if (this.httpRequest) {
      this.httpRequest.destroy();
    }
  }
}
```

### 4. Function Pages

Function Pages 是各具体功能实现的页面，盛放各自所需组件；

须从Home Page跳转；

本实验有2个Function Pages

- JSON Page
- Web Page

#### 4.1 JSON Page

##### 4.1.1 截图展示

![alt text](../JsonPage.png)

##### 4.1.2 代码实现

```typescript {.line-numbers}
// 导入路由模块
import router from '@ohos.router';

// 定义路由参数类型接口
interface JsonPageParams {
  jsonData: string;  // JSON数据字符串
}

// 入口组件装饰器
@Entry
// 组件装饰器
@Component
// 定义JsonPage结构体组件
struct JsonPage {
  // 状态变量：存储JSON数据
  @State jsonData: string = '暂无数据';
  // 状态变量：加载状态
  @State isLoading: boolean = true;

  // 组件即将显示的生命周期方法
  aboutToAppear() {
    // 从路由参数获取数据并类型断言为JsonPageParams
    const params = router.getParams() as JsonPageParams;

    // 检查是否有有效数据
    if (params?.jsonData) {
      // 模拟异步数据处理（300ms延迟）
      setTimeout(() => {
        this.jsonData = params.jsonData;  // 更新JSON数据
        this.isLoading = false;  // 结束加载状态
      }, 300); // 小延迟确保页面过渡动画完成
    } else {
      // 没有收到数据的处理
      this.jsonData = '未接收到有效数据';
      this.isLoading = false;
    }
  }

  // 构建UI
  build() {
    // 主容器
    Column() {
      // 加载状态判断
      if (this.isLoading) {
        // 显示加载指示器
        LoadingProgress()
          .width(50)
          .height(50)
      } else {
        // 使用可滚动容器展示JSON数据
        Scroll() {
          // 富文本组件显示格式化JSON
          RichText(this.jsonData)
            .width('100%')
            .height('100%')
            .padding(20)  // 内边距
            .backgroundColor('#f5f5f5')  // 浅灰色背景
            .borderRadius(10)  // 圆角边框
            // RichText渲染开始回调
            .onStart(() => {
              console.log('RichText渲染开始');
            })
            // RichText渲染完成回调
            .onComplete(() => {
              console.log('RichText渲染完成');
            })
        }
        .width('100%')
        .height('90%')  // 留出底部空间
      }
    }
    // 主容器样式
    .width('100%')
    .height('100%')
    .padding(10)  // 整体内边距
  }
}
```

#### 4.2 Web Page

##### 4.2.1 截图展示
![alt text](../Webpage.png)

##### 4.2.2 代码实现

```typescript {.line-numbers}
// 导入ArkUI路由模块
import { router } from '@kit.ArkUI';
// 导入WebView模块
import web_webview from '@ohos.web.webview';

// 入口组件装饰器
@Entry
// 组件装饰器
@Component
// 定义WebPage结构体组件
struct WebPage {
  // 创建WebviewController实例，用于控制Web组件
  private controller: web_webview.WebviewController = new web_webview.WebviewController();

  // 构建UI
  build() {
    // 主容器Column
    Column() {
      // Web组件，用于显示网页内容
      Web({
        src: $rawfile('Agricultural_Crop_Yields_WebApp.html'), // 加载rawfile目录下的本地HTML文件
        controller: this.controller // 绑定Web控制器
      })
      .width('100%')  // 宽度100%填充
      .height('100%')  // 高度100%填充
    }
    .width('100%')  // 容器宽度100%
    .height('100%')  // 容器高度100%
  }
}
```