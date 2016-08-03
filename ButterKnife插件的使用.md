# ButterKnife插件的使用
## 安装ButterKnife插件
在AndroidStudio设置中找到Plugins，然后搜索ButterKnife，下载安装，重启AndroidStudio。
## Gradle的相关配置
首先在最外层的gradle中添加一句 __classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'__

	buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.0'
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
	}

然后在项目的gradle中的最上面加入 __apply plugin: 'com.neenbedankt.android-apt'__

	apply plugin: 'com.android.application'
	apply plugin: 'com.neenbedankt.android-apt'

最后在**dependencies**中加入

	compile 'com.jakewharton:butterknife:8.2.1'
    apt 'com.jakewharton:butterknife-compiler:8.2.1'

至此ButterKnife的gragle相关配置已经完成。
## 使用ButterKnife
在需要初始化控件的地方，如Activity的**onCreate**方法中

	protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

将光标放在 **R.layout.activity_main**上，**Alt+insert** ，选择 **Generate ButterKnife Injections** 
，选择需要注解的控件以及是否onClick，确定后会自动在setContentView(R.layout.activity_main);
下面加上 **ButterKnife.bind(this);**。
