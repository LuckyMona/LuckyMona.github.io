---
title: ion-view、ng-repeat遇到$watch时产生的一个小坑
date: 2016-7-11 21:31:00
categories: 框架
tags: [ionic,angularJS,scope,ion-radio] #文章标签，可空，多标签请用格式，注意:后面有个空格
toc: true
comments: true
---
使用ionic的时候，有这样的一个场景，需要对ion-radio使用ng-rapeat，以生成一个选项列表，并当选择选项的时候，检测值的改变。选项列表是放在一个ion-view中的，这其中涉及多层scope的嵌套：controller有一个顶层scope，ion-view在渲染的时候，会生成一个子scope，ng-repeat又会生成自己的scope，多个scope嵌套起来非常复杂，实现的时候搞得比较久，记录下结果：
<!-- More -->

## 1、通过$state配置视图的controller 时的写法：##
### app.js配置： ###
```
angular.module('starter',['ionic'])
    .config(function($stateProvider, $urlRouterProvider){
        $stateProvider
            .state('floor',{  
                url: '/floor',
                templateUrl: 'templates/floor.html',
                controller: 'FloorCtrl'
                })

    });
```
### 模板部分写法： ###
```
<ion-view>
    <ion-content>
        <div>
            <ion-list>
                <ion-radio ng-model="a.floorModel" ng-repeat="floor in floorItems" value="{{floor}}">{{floor}}</ion-radio>
            </ion-list>
        </div>
    </ion-content>
</ion-view>
```

### controller部分： ###
```
angular.module('starter').controller('FloorCtrl', function($scope){
    $scope.a = {"floorModel":""}; 
    $scope.floorItems = ['floorA', 'floorB'];

    $scope.$watch("a.floorModel", function(newVal,oldVal){
        console.log('floorNewVal:'+ newVal);
        if(newVal==oldVal){
            return;
        }
        console.log('value change');
});
```

## 2、controller写在ion-content上可以减少一层scope  ##
### 模板部分写法： ###
```
<ion-view ng-controller="FloorCtrl">
    <ion-content>
        <div>
            <ion-list>
                <ion-radio ng-model="$parent.floorModel" ng-repeat="floor in floorItems" value="{{floor}}">{{floor}}</ion-radio>
            </ion-list>
        </div>
    </ion-content>
</ion-view>
```
### controller部分： ###
```
angular.module('starter').controller('FloorCtrl', function($scope){
    $scope.floorModel = ''; 
    $scope.floorItems = ['floorA', 'floorB'];

    $scope.$watch(floorModel, function(newVal,oldVal){
        console.log('floorNewVal:'+ newVal);
        if(newVal==oldVal){
            return;
        }
        console.log('value change');
});
```

---

## 大坑 ##
1. 写模板的时候，ion-radio标签的value按照[官网](http://ionicframework.com/docs/api/directive/ionRadio/)介绍作ng-value="xxx"时，会有这样的bug：第一次选择时，总是选列表的最后一项，并且$watch只在值为数字的时候表现正常，按照angularJS的radio的value写法value="xxx"，$watch就表现正常了，而其他页面却没有这个bug，目前实验多次均是这个结论，也算是个大坑了。~~也由此得到的**教训**：出现很奇葩的bug的时候，一定要仔细求证语句写法；~~ 这不能怪本宝宝。

2. 关于第二种写法，即controller写在ion-content上，如果没有用ng-repeat这种会生成child scope的用法时，就不用加$parent了；如果用了就必须加，因为嵌套了两层scope，而把controller写在ion-content上只消除一层scope嵌套。

## 小知识点 ##
当$watch的第一个参数写成字符串不起作用时，可以这样写，第一个参数写成函数：
```
$scope.$watch(function(){return $scope.a.floorModel}, function(newVal, oldVal){});
```

