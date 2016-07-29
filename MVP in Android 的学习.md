#MVP模式的学习
MVP的意思：

- Model 业务逻辑和实体模型
- View 对应于Activity，负责View的绘制以及与用户交互
- Presenter 负责完成View于Model间的交互

下面以一个简单的登录程序来体会一下MVP在Android中的设计思想：
<br>首先写Model层，需要一个实体类User：

	public class User {
    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
	}

User类中包括一个username和一个password。
<br>然后需要写一个用于操作登录的业务逻辑的接口，登录时需要username和password以及一个登录的监听者，直接传进去：

	public interface IUserBiz {
    public void login(String username, String password, OnLoginListener loginListener);
	}

这里需要用到一个登录的监听者，用于登录成功或失败时执行相应的业务，所以写一个接口，登录成功后需要将User对象传到指定的地方进行其他的业务操作，所以要传进去一个User对象：

	public interface OnLoginListener {
    void loginSuccess(User user);
    void loginFailed();
	}

接下来开始写登录的业务逻辑，新建一个UserBiz类，实现IUserBiz接口：

	public class UserBiz implements IUserBiz{
    @Override
    public void login(final String username, final String password, final OnLoginListener loginListener) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if ("123".equals(username) && "123".equals(password)){
                    User user = new User();
                    user.setUsername(username);
                    user.setPassword(password);
                    loginListener.loginSuccess(user);
                }else {
                    loginListener.loginFailed();
                }
            }
        }).start();
    }
	}

在这里模拟请求服务器耗时，所以加了个延时，当username和password验证成功时执行loginSuccess方法，并传入user对象，验证失败则执行相应的loginFailed方法。
<br>至此，登录的业务逻辑就写的差不多了，接下来开始写View层代码。
<br>Presenter与View是通过接口进行交互，所以需要先写一个ILoginView接口。这个接口中需要什么方法呢？思索一下，登录需要用到username和password，所以要有：

	String getUserName();
    String getPassWord();

登录是个耗时的操作，需要有进度条来友好的提示用户：

	void showLoading();
    void hideLoading();

登录成功或者失败后需要有相应的操作来跳转或者提示：

	void toMainActivity();
    void failed();

还需要一个clear来清除输入框中的username和password：

	void clearUserName();
    void clearPassWord();

知道需要的方法了，接口自然就写好了：

	public interface ILoginView {
    String getUserName();
    String getPassWord();
    void toMainActivity();
    void failed();
    void showLoading();
    void hideLoading();
    void clearUserName();
    void clearPassWord();
	}

总结下，对于View的接口，去观察功能上的操作，然后考虑：

- 该操作需要什么？（getUserName, getPassword）
- 该操作的结果，对应的反馈？(toMainActivity, failed)
- 该操作过程中对应的友好的交互？(showLoading, hideLoading)

下面开始写View的实现类，也就是Activity的操作：

	public class MainActivity extends AppCompatActivity implements ILoginView{

    private EditText usernameText;
    private EditText passwordText;
    private Button loginBut;
    private Button clearBut;
    private ProgressBar progressBar;
    private UserLoginPresenter mUserLoginPresenter = new UserLoginPresenter(this);
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView() {
        usernameText = (EditText) findViewById(R.id.username);
        passwordText = (EditText) findViewById(R.id.password);
        loginBut = (Button) findViewById(R.id.login);
        clearBut = (Button) findViewById(R.id.clear);
        progressBar = (ProgressBar) findViewById(R.id.progressBar);
        loginBut.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mUserLoginPresenter.login();
            }
        });
        clearBut.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mUserLoginPresenter.clear();
            }
        });
    }

    @Override
    public String getUserName() {
        return usernameText.getText().toString();
    }

    @Override
    public String getPassWord() {
        return passwordText.getText().toString();
    }

    @Override
    public void toMainActivity() {
        Toast.makeText(MainActivity.this, "success", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void failed() {
        Toast.makeText(MainActivity.this, "failed", Toast.LENGTH_SHORT).show();

    }

    @Override
    public void showLoading() {
        progressBar.setVisibility(View.VISIBLE);
    }

    @Override
    public void hideLoading() {
        progressBar.setVisibility(View.GONE);

    }

    @Override
    public void clearUserName() {
        usernameText.setText("");
    }

    @Override
    public void clearPassWord() {
        passwordText.setText("");
    }
	}

首先Activity需要实现ILoginView接口，然后实现接口中相应的方法。然后得到Presenter类的对象，
执行其中的login方法。
最后是Presenter：

	public class UserLoginPresenter {
    private ILoginView loginView;
    private IUserBiz userBiz;
    private Handler mHandler = new Handler();
    public UserLoginPresenter(ILoginView loginView) {
        this.loginView = loginView;
        this.userBiz = new UserBiz();
    }
    public void login(){
        loginView.showLoading();
        userBiz.login(loginView.getUserName(), loginView.getPassWord(), new OnLoginListener() {
            @Override
            public void loginSuccess(User user) {
                mHandler.post(new Runnable() {  //通过Handler在UI线程中操作UI变化
                    @Override
                    public void run() {
                        loginView.toMainActivity();
                        loginView.hideLoading();
                    }
                });
            }

            @Override
            public void loginFailed() {
                mHandler.post(new Runnable() {
                    @Override
                    public void run() {
                        loginView.failed();
                        loginView.hideLoading();
                    }
                });
            }
        });
    }
    public void clear(){
        loginView.clearUserName();
        loginView.clearPassWord();
    }
	}

首先需要传入ILoginView接口的实现类也就是Activity，然后得到IUserBiz接口实现类的对象，执行其login方法，
传入相应的参数，实现OnLoginListener接口回调，在相应的成功和失败方法中执行相应的操作，这里注意
需要通过Handler.post()在UI线程中操作UI，如进度条的隐藏和Toast的显示。最后实现clear方法。
### 总结一下
使用MVP设计模式需要分这几个步骤：

- Model层：首先写实体类，然后定义操作业务逻辑的接口以及相应的事件监听者，最后实现业务逻辑类。
- View层： 分析View接口需要的方法，定义View的实现接口，在Activity中实现接口，通过Presenter
对象来操作其中的方法，以达到Model与View之间的调解。
- Presenter层：得到View的接口实现类对象（一般是Activity），执行View接口中的方法，得到Model
的业务逻辑类对象，执行相应的方法。这样就达到了Model与View之间的调解。
