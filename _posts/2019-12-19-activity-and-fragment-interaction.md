---
layout: post
title: Activity和Fragment 交互
date: 2019-12-19
Author: Zhu Kun
tags: [Fragment, Activity]
toc: true
comments: true
---

## Activity调用 Fragment

1. [初始化传参](onenote:#Fragment构造参数传参&section-id={4DEA1A81-5A6F-CC40-8F58-51AB33411D8C}&page-id={0469E712-E9E9-441C-851B-1B441F258272}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Fragment.one)
2. 广播发送监听
3. EventBus消息总线发送监听
4. 单例模式处理



## Fragment 调用 Activity

1. 直接调用（MainActivity     mainActivity=(MainActivity) getActivity()）

2. 接口回调（官方推荐）

   **Fragment** 中定义一个接口

   ```java
   public class CabinetFloorPlanFragment extends Fragment {
       private FragmentCabinetFloorPlanBinding binding;
   
       private OnFragmentInteractionListener mListener;
   
       public CabinetFloorPlanFragment() {
           // Required empty public constructor
       }
       @Override
       public void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
       }
   
       @Override
       public View onCreateView(LayoutInflater inflater, ViewGroup container,
                                Bundle savedInstanceState) {
           // Inflate the layout for this fragment
           binding = DataBindingUtil.inflate(inflater, R.layout.fragment_cabinet_floor_plan, container, false);
           return binding.getRoot();
       }
   
       // TODO: Rename method, update argument and hook method into UI event
       public void onButtonPressed(Uri uri) {
           if (mListener != null) {
               mListener.onFragmentInteraction(uri);
           }
       }
   
       @Override
       public void onAttach(Context context) {
           super.onAttach(context);
           if (context instanceof OnFragmentInteractionListener) {
               mListener = (OnFragmentInteractionListener) context;
           } else {
               throw new RuntimeException(context.toString()
                       + " must implement OnFragmentInteractionListener");
           }
       }
   
       @Override
       public void onDetach() {
           super.onDetach();
           mListener = null;
       }
   
       /**
        * This interface must be implemented by activities that contain this
        * fragment to allow an interaction in this fragment to be communicated
        * to the activity and potentially other fragments contained in that
        * activity.
        * <p>
        * See the Android Training lesson <a href=
        * "http://developer.android.com/training/basics/fragments/communicating.html"
        * >Communicating with Other Fragments</a> for more information.
        */
       public interface OnFragmentInteractionListener {
           // TODO: Update argument type and name
           void onFragmentInteraction(Uri uri);
       }
   }
   ```

   **Activity** 实现这个接口

   ```java
   public class CabinetFloorPlanActivity extends AppCompatActivity implements CabinetFloorPlanFragment.OnFragmentInteractionListener{
       
       @Override
       public void onFragmentInteraction(Uri uri) {
   
       }
   }
   ```

   

3. 广播发送监听

4. EventBus消息总线发送监听

5. 单例模式处理