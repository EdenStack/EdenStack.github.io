---
layout:     post
title:      Android MVP
date:       2015-10-14 14:50:18
summary:    Android MVP
author:     Tneciv
categories: android
thumbnail: android
tags:
 - Android MVP
---


android MVP：

activity中只进行view初始化等操作，并实现interface \*\*\*view

````java

public class LoginActivity extends ActionBarActivity implements ILoginView, View.OnClickListener {

	private EditText editUser;
	private EditText editPass;
	private Button   btnLogin;
	private Button   btnClear;
	ILoginPresenter loginPresenter;
	private ProgressBar progressBar;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

		//find view
		editUser = (EditText) this.findViewById(R.id.et_login_username);
		editPass = (EditText) this.findViewById(R.id.et_login_password);
		btnLogin = (Button) this.findViewById(R.id.btn_login_login);
		btnClear = (Button) this.findViewById(R.id.btn_login_clear);
		progressBar = (ProgressBar) this.findViewById(R.id.progress_login);

		//set listener
		btnLogin.setOnClickListener(this);
		btnClear.setOnClickListener(this);

		//init
		loginPresenter = new LoginPresenterCompl(this);
		loginPresenter.setProgressBarVisiblity(View.INVISIBLE);
	}

	@Override
	public void onClick(View v) {
		switch (v.getId()){
			case R.id.btn_login_clear:
				loginPresenter.clear();
				break;
			case R.id.btn_login_login:
				loginPresenter.setProgressBarVisiblity(View.VISIBLE);
				btnLogin.setEnabled(false);
				btnClear.setEnabled(false);
				loginPresenter.doLogin(editUser.getText().toString(), editPass.getText().toString());
				break;
		}
	}

	@Override
	public void onClearText() {
		editUser.setText("");
		editPass.setText("");
	}

	@Override
	public void onLoginResult(Boolean result, int code) {
		loginPresenter.setProgressBarVisiblity(View.INVISIBLE);
		btnLogin.setEnabled(true);
		btnClear.setEnabled(true);
		if (result){
			Toast.makeText(this,"Login Success",Toast.LENGTH_SHORT).show();
			startActivity(new Intent(this, HomeActivity.class));
		}
		else
			Toast.makeText(this,"Login Fail, code = " + code,Toast.LENGTH_SHORT).show();
	}


	@Override
	public void onSetProgressBarVisibility(int visibility) {
		progressBar.setVisibility(visibility);
	}
}

````

interface \*\*\*Presenter中进行业务逻辑处理：

````java

public interface ILoginPresenter {
	void clear();
	void doLogin(String name, String passwd);
	void setProgressBarVisiblity(int visiblity);
}

````

LoginPresenterCompl.java实现类

````java

public class LoginPresenterCompl implements ILoginPresenter {
	ILoginView iLoginView;
	IUser user;
	Handler    handler;

	public LoginPresenterCompl(ILoginView iLoginView) {
		this.iLoginView = iLoginView;
		initUser();
		handler = new Handler(Looper.getMainLooper());
	}

	@Override
	public void clear() {
		iLoginView.onClearText();
	}

	@Override
	public void doLogin(String name, String passwd) {
		Boolean isLoginSuccess = true;
		final int code = user.checkUserValidity(name,passwd);
		if (code!=0) isLoginSuccess = false;
		final Boolean result = isLoginSuccess;
		handler.postDelayed(new Runnable() {
			@Override
			public void run() {
				iLoginView.onLoginResult(result, code);
			}
		}, 3000);

	}

	@Override
	public void setProgressBarVisiblity(int visiblity){
		iLoginView.onSetProgressBarVisibility(visiblity);
	}

	private void initUser(){
		user = new UserModel("mvp","mvp");
	}
}

````

ILoginView 接口（activity作为实现类）

````java

public interface ILoginView {
	public void onClearText();
	public void onLoginResult(Boolean result, int code);
	public void onSetProgressBarVisibility(int visibility);
}

````
