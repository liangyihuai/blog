---
layout: post
title: 出操队形
date: 2016-12-23 12:15
comments: true
external-url:
categories: Algorithm
---

This is a blog on algorithms.

问题描述：

*在读高中的时候，每天早上学校都要组织全校的师生进行跑步来锻炼身体，每当出操令吹响时，大家就开始往楼下跑了，然后身高矮的排在队伍的前面，身高较高的就要排在队尾。突然，有一天出操负责人想了一个主意，想要变换一下队形，就是当大家都从楼上跑下来后，所有的学生都随机地占在一排，然后出操负责人从队伍中抽取出一部分学生，使得队伍中剩余的学生的身高从前往后看，是一个先升高后下降的“山峰”形状。据说这样的形状能够给大家带来好运，祝愿大家在学习的道路上勇攀高峰。(注，山峰只有一边也符合条件，如1,1、2,2、1均符合条件)*



输入：

```
输入可能包含多个测试样例。
对于每个测试案例，输入的第一行是一个整数n(1<=n<=1000000)：代表将要输入的学生个数。
输入的第二行包括n个整数：代表学生的身高（cm）(身高为不高于200的正整数)。
```

```
输出：
对应每个测试案例，输出需要抽出的最少学生人数。
```

样例输入输出：

```
6
100 154 167 159 132 105
5
152 152 152 152 152
样例输出：
0
4
```

*** 

*第一个版本*

***

## 1-1. 定义：
1. 凹点的定义：一组数组中小于左右邻居的点。第一个点和最后一个点也算是凹点。
2. 山峰的定义：任何一个点都可以构成一个山峰，但是这里为了算法的实现方便，我们把任何两个凹点和它们俩之间的所有点定义为一个山峰。
3. 山峰大小的定义：山峰中的所有点中没有凹点。比如：1、2、3、2、4、5、4、3、2、1，所以第一个点和第10个点所构成的山峰的点是1、2、3、4、5、4、3、2、1，没有包含第四个点，因为它是凹点。


> 原始的问题：从一个组数据中抽取出若干的元素，使得这一组数组构成一座山峰。要求抽取的元素个数最小。

> 问题转化：把这一组数据比作很多座连在一起的山峰，每两个凹点都可以构成一座山，计算哪一座山最大。（山峰大小的定义在上面已经给出）

## 1-2. 算法：

1. 读取数据，转换成一个数组。
2. 去掉相邻相同的重复的数据，比如1、2、3、3、2、1、1，结果为：1、2、3、2、1
3. 找到和保存所有的凹点。比如：8 、9、 12、 10、 13、 8、 6、 7、 14、 10，凹点为第一个、第四个、第六个和最后一个。
4. 计算每一座山峰的大小，保存最大的。（可以使用两个for循环遍历凹点的集合）
5. 返回原始的数组长度和最大的山峰长度的差值。



## 1-3. 复杂度：

1. 时间复杂度：0(n^3)

   在第三步使用了两个for循环找到所有两个凹点的组合，计算山峰的大小需要0(n)的时间复杂度。

2. 空间复杂度：0(n)

   使用了0（n）的空间存储凹点。

***

*第二个版本*

***

## 2-1. 定义

1. 两个山峰a和b的强弱比较：如果山峰a在山峰b的左边，那么a的第一个点到最高点的非凹点的个数如果比b的多，表示a比b强，否则a比b弱。

2. `start ~ end`表示以第一个下标为start，最后一个下标为end的所有点所构成的山峰。

## 2-2. 算法

1. 读取数据，转换成一个数组。
2. 去掉相邻相同的重复的数据，比如1、2、3、3、2、1、1，结果为：1、2、3、2、1
3. 找到和保存所有的凹点。比如：8 、9、 12、 10、 13、 8、 6、 7、 14、 10，凹点为第一个、第四个、第六个和最后一个。
4. 设置两个指针start和end，分别指向凹点数组的第一个点和最后一个点。计算和存储以这两个指针为边界的山峰的大小，表示为`start ~ end`。如果是当前最大的，更新最大的山峰。
5. 计算`start ～ (start+1)山峰`和`(end-1) ～ end 山峰`的大小，如果是当前最大的，更新最大的山峰。
5. 比较`start ～ (start+1)山峰`和`(end-1) ～ end 山峰`的强弱，如果`start ～ (start+1)山峰`比`(end-1) ～ end山峰`强，则end++，否则start--。
5. 返回原始的数组长度和最大的山峰长度的差值。

## 2-3. 复杂度

1. 时间复杂度：0（n^2）

   第四部中需要两个start和end指针，分别从前面和后面往中间遍历，时间复杂度为0（n）， 计算山峰的大小和计算山峰的强弱的时间复杂度为0（n）
   
2. 空间复杂度：0（n）

   使用了0（n）的空间来存储凹点。

***

## 第一个版本源码：

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;

/**
 * Created by liangyh on 2016/12/21.
 */
public class MountainShapeTest {

    /**
     * 用来存储凹点,所谓凹点，指的是这个点小于它左右邻居的值。第一个点和最后一个点也算是凹点。
     */
    private List<Integer> lowestPoints = new ArrayList<Integer>();



    public int extract(List<Integer> highList){
        if(highList == null || highList.size() == 0)return 0;

        List<Integer> remainList = removeNeighbourSame(highList);
        setLowestPoints(remainList);

        int remainNum = 1;

        for(int i = 0; i < lowestPoints.size()-1; i++){
            for(int k = i+1; k < lowestPoints.size(); k++){
                remainNum = Math.max(remainNum, getHigherNum(lowestPoints.get(i), lowestPoints.get(k), remainList));
            }
        }

        return highList.size()-remainNum;
    }

    //去掉重复，相邻并且值相同的两个元素
    private List<Integer> removeNeighbourSame(List<Integer> highList){
        List<Integer> result = new ArrayList<>();
        if(highList == null || highList.size() == 0)return result;
        int prev = highList.get(0);
        result.add(0);
        for(int i = 1; i < highList.size(); i++){
            int cur = highList.get(i);

            if(prev == cur) continue;

            result.add(cur);
            prev = cur;
        }
        return result;
    }

    /**
     * 获取并设置凹点，所谓凹点，指的是这个点小于它左右相邻的值。第一个点和最后一个点也算是凹点。
     * @param list
     */
    private void setLowestPoints(List<Integer> list){
        if (list == null || list.size() == 0)return ;
        if(list.size() == 1){
            lowestPoints.add(0);
            return ;
        }
        lowestPoints.add(0);
        for(int i = 1; i < list.size() - 1; i++){
            if(list.get(i) < list.get(i-1) && list.get(i) < list.get(i+1)){
                lowestPoints.add(i);
            }
        }
        lowestPoints.add(list.size()-1);
    }

    //在start和end之间，统计比下标为start和end的元素大的数量。它们组成一个山峰。
    // 比如：数组8 9 12 10 13，start为0， end为4， 那么返回值为4， 因为10是不被包含的。
    private int getHigherNum(int start, int end, List<Integer> list){
        if(start > end) return 0;
        if(start == end)return 1;

        int result = 2;
        int cur1 = start;
        int cur2 = end;
        int s = start+1;
        int e = end-1;

        while(s <= e){
            if(list.get(s) > list.get(cur1)){
                result++;
                cur1 = s;
            }
            s++;

            if(s > e) break;

            if(list.get(e) > list.get(cur2)){
                cur2 = e;
                result++;
            }
            e--;
        }
        return result;
    }

    private int countUntilTop_left(int start, int end, List<Integer> list){

    }

    private int countUntilTop_right(int start, int end, List<Integer> list){

    }

    /**
     6
     100 154 167 159 132 105 (抽取0个)

     5
     152 152 152 152 152(抽取4个)

     10
     8 9 12 10 13 8 6 7 14 10 (4)

     8
     8 9 12 10 13 8 6 7 (2)

     8
     9 12 10 13 8 6 7 14 (抽取3个)，剩下的是：9 10 13 8 6
     * @param args
     */
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        scanner.nextLine();

        List<Integer> list = new ArrayList<>(n);
        for(int i = 0; i < n; i++){
            list.add(scanner.nextInt());
        }

        int result = new MountainShapeTest().extract(list);
        System.out.println(result);

    }
}

```

## 第二个版本的源码

```
package com.huai.test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Scanner;

/**
 * Created by liangyh on 2016/12/21.
 */
public class MountainShapeTest {

    /**
     * 用来存储凹点,所谓凹点，指的是这个点小于它左右邻居的值。第一个点和最后一个点也算是凹点。
     */
    private List<Integer> lowestPoints = new ArrayList<Integer>();

    //第二个版本，时间复杂度为0(n^2)，空间复杂度为0（n）
    public int extract2(List<Integer> highList){
        if(highList == null || highList.size() == 0)return 0;
        List<Integer> remainList = removeNeighbourSame(highList);
        setLowestPoints(remainList);

        int remainNum = 1;
        //只有一个凹点，最多只有两个点（第一个点和最后一个点），最少只有一个点。
        if(lowestPoints.size() <= 2){
            return highList.size() - remainList.size();
        }else{
            int start = 0;
            int end = lowestPoints.size()-1;
            while(start < end){
                remainNum = Math.max(remainNum, getHigherNum(lowestPoints.get(start), lowestPoints.get(end), remainList));
                if(start+1 <= end-1){
                    int num1 = getHigherNum(lowestPoints.get(start), lowestPoints.get(start+1), remainList);
                    int num2 = getHigherNum(lowestPoints.get(end-1), lowestPoints.get(end), remainList);

                    remainNum = Math.max(remainNum, num1);
                    remainNum = Math.max(remainNum, num2);

                    //如果所遍历到的两个点重合了，说明可以结束了。
                    if(start+1 == end-1) break;
                    
                    int num3 = countUntilTopFromLeft(lowestPoints.get(start), lowestPoints.get(start+1), remainList);
                    int num4 = countUntilTopFromRight(lowestPoints.get(end-1), lowestPoints.get(end), remainList);
                    if(num3 > num4){
                        end--;
                    }else{
                        start++;
                    }
                }
            }
        }
        return highList.size() - remainNum;
    }

    //去掉重复，相邻并且值相同的两个元素
    private List<Integer> removeNeighbourSame(List<Integer> highList){
        List<Integer> result = new ArrayList<>();
        if(highList == null || highList.size() == 0)return result;
        int prev = highList.get(0);
        result.add(0);
        for(int i = 1; i < highList.size(); i++){
            int cur = highList.get(i);

            if(prev == cur) continue;

            result.add(cur);
            prev = cur;
        }
        return result;
    }

    /**
     * 获取并设置凹点，所谓凹点，指的是这个点小于它左右相邻的值。第一个点和最后一个点也算是凹点。
     * @param list
     */
    private void setLowestPoints(List<Integer> list){
        if (list == null || list.size() == 0)return ;
        if(list.size() == 1){
            lowestPoints.add(0);
            return ;
        }
        lowestPoints.add(0);
        for(int i = 1; i < list.size() - 1; i++){
            if(list.get(i) < list.get(i-1) && list.get(i) < list.get(i+1)){
                lowestPoints.add(i);
            }
        }
        lowestPoints.add(list.size()-1);
    }

    /**
     * 在start和end之间，统计比下标为start和end的元素大的数量。它们组成一个山峰。
     * 比如：数组8 9 12 10 13，start为0， end为4， 那么返回值为4， 因为10是不被包含的。
     * @param start
     * @param end
     * @param list
     * @return
     */
    private int getHigherNum(int start, int end, List<Integer> list){
        if(start > end) return 0;
        if(start == end)return 1;

        int result = 2;
        int cur1 = start;
        int cur2 = end;
        int s = start+1;
        int e = end-1;

        while(s <= e){
            if(list.get(s) > list.get(cur1)){
                result++;
                cur1 = s;
            }
            s++;

            if(s > e) break;

            if(list.get(e) > list.get(cur2)){
                cur2 = e;
                result++;
            }
            e--;
        }
        return result;
    }

    /**
     * 从左边第一个点开始到最高点的非凹点的个数。
     * @param start
     * @param end
     * @param list
     * @return
     */
    public int countUntilTopFromLeft(int start, int end, List<Integer> list){
        if(start > end)return 0;
        if(start == end)return 1;

        int result = 1;
        int s = start;
        int e = end;
        int cur = list.get(start);
        for(int i = start+1; i <= end; i++){
            if(list.get(i) > cur){
                result++;
                cur = list.get(i);
            }
        }
        return result;
    }

    /**
     * 从右边第一个点开始到最高点的非凹点的个数。
     * @param start 第一个点
     * @param end 最后一个点
     * @param list
     * @return
     */
    public int countUntilTopFromRight(int start, int end, List<Integer> list){
        if(start > end)return 0;
        if(start == end)return 1;

        int result = 1;
        int s = start;
        int e = end;

        int cur = list.get(end);
        for(int i = end - 1; i >= start; i--){
            if(list.get(i) > cur){
                result++;
                cur = list.get(i);
            }
        }
        return result;
    }


    /**
     6
     100 154 167 159 132 105 (抽取0个)

     5
     152 152 152 152 152(抽取4个)

     10
     8 9 12 10 13 8 6 7 14 10 (4)

     8
     8 9 12 10 13 8 6 7 (2)

     8
     9 12 10 13 8 6 7 14 (抽取3个)，剩下的是：9 10 13 8 6
     * @param args
     */
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        scanner.nextLine();

        List<Integer> list = new ArrayList<>(n);
        for(int i = 0; i < n; i++){
            list.add(scanner.nextInt());
        }

        int result = new MountainShapeTest().extract2(list);
        System.out.println(result);

    }
}

```
