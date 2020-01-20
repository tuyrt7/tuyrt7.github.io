---
title: 被fragment覆盖的控件显示在上层问题
date: 2019-09-19 19:47:19
tags:
	- blog
	- ui
	- andorid
---


顶层布局ConstranitLayout,之后直接一个NaviHostFragment，其他控件和fragment平级布置


## 显示时fragment：  

![fragment内容](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/cover-1.jpg) 

## 切换fragment之后的布局：  


![fragment平级内容](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/cover-2.jpg) 

<!--more-->

### 代码： 

显示控件：注释1设置了图片资源，注释2再执行 `animate()` 之后才会显示

```  
 // 显示主界面提示语
    private void showSimpleTips() {
        mStatusHidden = false;
        setSimpleClick(true);

        mIvLogo.setOnClickListener(tapToTalkClickListener);
        mIvLogo.setImageResource(R.drawable.ic_voice_bar_regular_white_32dp);//1
        mIvLogo.animate().scaleX(1f).scaleY(1f).setDuration(200).setStartDelay(150).start();//2

        updateBottomBar();
        mTvTipsSimple.setOnClickListener(tapToTalkClickListener);
        mTvTipsSimple.animate().alpha(1f).setDuration(200).setStartDelay(150).start();

    }  

```

隐藏控件：注意注释1处和2处 ,透明度设为0，平移出，缩小为0 还是通过动画去控制

````

 private void hideIatPage() {
        mIvBlur.animate().alpha(0f).setStartDelay(100).setDuration(200).start();//1

        AnimationSet animationSet = new AnimationSet(true);
        TranslateAnimation translation = new TranslateAnimation(0f, -((mIvBlur.getWidth() / 8)), 0f, 0f);
        animationSet.addAnimation(translation);
        AlphaAnimation alphaAnimation = new AlphaAnimation(1f, 0f);
        animationSet.addAnimation(alphaAnimation);
        animationSet.setDuration(400);
        animationSet.setInterpolator(new FastOutLinearInInterpolator());
        animationSet.setAnimationListener(new Animation.AnimationListener() {
            @Override
            public void onAnimationStart(Animation animation) {

            }

            @Override
            public void onAnimationEnd(Animation animation) {
                mTipsTitle.setAlpha(0f);
                mTips1.setAlpha(0f);
                mTips2.setAlpha(0f);
                mTips3.setAlpha(0f);

            }

            @Override
            public void onAnimationRepeat(Animation animation) {

            }
        });
        mTipsTitle.startAnimation(animationSet);//2
        mTips1.startAnimation(animationSet);
        mTips2.startAnimation(animationSet);
        mTips3.startAnimation(animationSet);

        mIatLogo.setClickable(false);
        mIatLogo.animate().scaleX(0f).scaleY(0f).setDuration(200).setStartDelay(150).start();
        /*ScaleAnimation scaleAnimation = new ScaleAnimation(1f, 0f, 1f, 0f, (mIatLogo.getWidth() / 2), (mIatLogo.getHeight() / 2));
        scaleAnimation.setDuration(200);
        scaleAnimation.setAnimationListener(new Animation.AnimationListener() {
            @Override
            public void onAnimationStart(Animation animation) {

            }

            @Override
            public void onAnimationEnd(Animation animation) {
                mIatLogo.setScaleX(0f);
                mIatLogo.setScaleX(0f);
            }

            @Override
            public void onAnimationRepeat(Animation animation) {

            }
        });
        mIatLogo.startAnimation(scaleAnimation);
        */

        mIatText.animate().alpha(0f).setStartDelay(150).setDuration(200).start();
    }  

```


此问题好像之前工作的项目也遇到过，特此记录。

**暂时不清楚其中原理，待有空之后在回过来研究下**

工作几年后，间隔半年写代码，发现生疏好多，尽管之前复习敲了半个月的基础代码，把《第一行代码》敲完，从找工作到工作时有些还是有的会忘记，只能时常捡起来多看看。
