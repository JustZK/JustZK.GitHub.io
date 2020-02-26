---
layout: post
title: Activity中显示对话框或弹出浮层时，尽量使用DialogFragment，而非Dialog/AlertDialog
date: 2020-02-26
Author: Zhu Kun
tags: [Dialog, View]
toc: true
comments: true
---

在Activity中显示对话框或弹出浮层时，尽量使用DialogFragment，而非Dialog/AlertDialog，这样便于随Activity生命周期管理对话框/弹出浮层的生命周期。

正例：

```java
public void showPromptDialog(String text) {

    DialogFragment promptDialog = new DialogFragment() {

        @Override

        public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
            getDialog().requestWindowFeature(Window.FEATURE_NO_TITLE);
            View view = inflater.inflate(R.layout.fragment_prompt, container);
            return view;
        }
        promptDialog.show(getFragmentManager(),text);

    }

}
```

