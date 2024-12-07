---
title:  小程序积分排行
date:   2022-03-02 16:09:30 +0800
description: 小程序云开发积分管理，奖励自动排名定等级.
tags: [wechat, mini programs, node.js, cloud development]
categories: [JavaScript]
mathjax: yes
toc: yes 
key: 2022-03-02-wechat-miniprogram-rank-auto-order
---

企业微信里写了个小程序，对学生的积分进行管理，前段时间无聊想着弄个排名吧，因为平时也有扣分，那些平时表现好的犯错跟其他成员扣分一样扣，总觉得亏了点，于是就抄王者荣耀搞了奖分等级，等级高点的扣分的时候就打个折。懒得改小程序，就每个月导出数据库统计一次，扣分打折根据统计出来的排名折扣口算输入。新学期，想着长痛不如短痛，还是直接做在小程序里好了。
<!--more-->
## 云数据库新建rank集合
 字段一开始只有name，reward，graduate三个个字段，后来发现坑了，哪怕数据权限改成“所有用户可读，仅创建者可读写”也无法修改数据，只好再加了个_openid字段，值全部是我自己的openid，然后才正常了，也就是加了openid我才有权限修改，这里折腾了很久，一直以为是自己逻辑弄错了。
## pages/admin/admin.js	//奖惩处理
### 数据库初始化
```javascript
// score集合保存所有学生的奖惩记录
const score = db.collection('score');
const rankCollection = db.collection('rank');
```
### 输入姓名从rank集合读取排名计算扣分时的折扣rate
 这里我傻了点，我是用for循环找有序数组里对应用户的排名的，其实直接在取好的数组里添加一个顺序字段index，后面直接读取index就可以了。
	 
```javascript
// 从rank集合按降序提取前10名未毕业的同学
const rank10 = db.collection('rank').where({
  graduate: 0
}).limit(10).orderBy('reward', 'desc');
```	
```javascript
// 奖惩时输入姓名查找前十名次，计算折扣 
  inputName(event) {
    nowname = event.detail
      rank10.get({
        success: res => {
          this.setData({
            rate: 1
          })
          for (let i = 0; i < 10; i++) {  //查找排名
            if( res.data[i].name == event.detail){
              if (i < 1){var rate = 0.5;}
                  else if (i < 3){var rate = 0.7;}
                    else if(i < 6){var rate = 0.8;}
                      else {var rate = 0.9;}
              console.log(rate)
              this.setData({
                rate: rate
              })
              break
            }
          }
        }
      })
  },
```
### 奖惩操作：负分打折，正分累加
参考[小程序调用另一个函数方法中的值](https://blog.csdn.net/Shids_/article/details/110142163)调用rate，保存数据时判断分数小于0，则乘以rate。累加那段折腾了很久，一开始用的get.where判断用success箭头函数，但就是要么不生效要么新增数据两倍累加，也不懂，就不停试，直到后来看到[JavaScript 错误 - Throw 和 Try to Catch (w3school.com.cn)](https://www.w3school.com.cn/js/js_errors.asp)，试了下，好像可以，想着里面应该是个函数，确实可行，就先这样了。中间还出了个拼写错误，现在就不知道试的过程中还有没有别的方法是可以用的了。
```javascript
  // 添加奖惩信息
  saveuserinfo() {
    if (score1 < 0){ //负分处理
      var rate = this.data.rate
      score.add({  //添加折后数据
            data: {
              name: nowname,
              score: score1*rate, //扣分打折
              detail: detail,
              content: content,
              time: time
            }
          }).then(res => {
            console.log('奖惩成功！')
            wx.showToast({
              title: '奖惩成功！',
              icon: 'success',
              duration: 3000
            })
          })
        }
    else{	//正分处理
      score.add({  //添加奖分数据
        data: {
          name: nowname,
          score: score1, //奖分不打折
          detail: detail,
          content: content,
          time: time
        }
      }).then(res => {
        console.log('奖惩成功！')
        wx.showToast({
          title: '奖惩成功！',
          icon: 'success',
          duration: 3000
        })
      }) 
      //加分写入rank集合，if的几种情况不累加，不知道怎么or。。。
      if(content == "申请通过"){} 
      else if(content == "申请成功"){}
      else if(content == "战队排行"){}
      else if(content == "提交检讨"){}
      else{ //把加分累加到总奖分里
        console.log(nowname,score1)
        //先添加数据，数据库name唯一索引，已有name会出错，错误时更新reward
        rankCollection.add({  
        data: {
          graduate: 0,	//既然有加分那肯定没毕业
          name: nowname,
          reward: score1
        }
        }).catch(function() {
          console.log('已有用户数据,更新总奖分');
          rankCollection.where({
            name: nowname
          }).update({
            data: {
              reward: _.inc(score1)
            }
          }) 
        })
      }
    }
  }   
,
```
## pages/index/index.js	//首页等级逻辑

```javascript
// 读取当前用户在前十名中的名次，未匹配则为青铜
console.log('[获取]user集合用户姓名: ', nowname)
rankCollection.where({ //毕业学生全部都是传奇王者
  name: nowname
  })
  .get({
    success: res => {
      if (res.data[0].graduate == 1)
      {this.setData({
        grade: "☆☆☆☆☆传奇王者☆☆☆☆☆"
      })
      console.log('等级: ', grade)
      }
      else {
        rank10.get({
          success: res => {
            this.setData({
              grade: "★倔强青铜★"	//在校生默认青铜
            })
            for (let i = 0; i < 10; i++) {  //查找前十名次
              if( res.data[i].name == nowname){
                if (i < 1){this.setData({
                        grade: "★★★★★荣耀王者★★★★★"
                      })}
                else if (i < 3){this.setData({
                      grade: "★★★★至尊星耀★★★★"
                    })}
                  else if(i < 6){this.setData({
                        grade: "★★★永恒钻石★★★"
                      })}
                else {this.setData({
                  grade: "★★尊贵铂金★★"
                  })}
                console.log(grade)
                break
              }
            }
          }
        })
      }
    },
    fail:	//从来没加过分的同学都是青铜
    this.setData({
      grade: "★倔强青铜★"
    })
  })
```
## pages/index/index.wxml	//首页渲染
 用了个[Vant Weapp](https://youzan.github.io/vant-weapp/#/quickstart)的UI组件vantvan-button展示等级，CSS我不知道怎么修改，就只好挑自己喜欢的组件用了。
```xml
<van-button type="warning" size="large">{{grade}}</van-button>
```
## 前十名次计算的另一种思路
用for循环增加index名次字段，然后读取名次就好了，做前二十的排行榜的时候网上找的办法。

 按前面的方法，rank20只有名字和总奖分，渲染的页面就是一个ranklist循环：
 ```xml
<!--pages/rank/rank.wxml-->
<block wx:for="{{ranklist}}" wx:key="_id">
<van-cell value="{{item.reward}}" center>
  <view slot="title">
    <view class="van-cell-text">{{item.index}}.  {{item.name }}</view>
    <van-tag type="danger">{{item.grade}}</van-tag>
  </view>
</van-cell>
</block>
```
不知道怎么在wxml加其他要展示的信息，那就只能在rank.js里想办法了。
pages/rank/rank.js：for循环添加名次index和等级grade字段到ranklist。
```javascript
const rank20 = db.collection('rank').where({
  graduate: 0
}).limit(20).orderBy('reward', 'desc');

rank20.get()
.then(async res => {
  var ranklist = res.data
  // console.log(ranklist)
    for (const i in ranklist) {
      ranklist[i].index = Number(i)+1
      if (Number(i) < 1){ranklist[i].grade = "★★★★★荣耀王者★★★★★"}
      else if (Number(i) < 3){ranklist[i].grade =  "★★★★至尊星耀★★★★"}
      else if(Number(i) < 6){ranklist[i].grade = "★★★永恒钻石★★★"}
      else  if(Number(i) < 10){ranklist[i].grade = "★★尊贵铂金★★"}
      else{ranklist[i].grade = "★倔强青铜★"}
    }
      // console.log(ranklist)  
      this.setData({
        ranklist: ranklist
      }) 
})
```
## 结语
开学一个礼拜就干了这个，换成懂的人，估计几分钟就解决了吧，那几天脑子里都在想为什么写的跟实现的不一样，怎么才能解决，然后就是各种搜索。官方文档确实很有用，有些问题就是直接抄的官方文档里的代码稍微改改就可以了。

挺好的，实现了自己想要的功能，很有成就感。
