---
title: Redis + Mongodb 点赞操作
tags: []
id: '121'
categories:
  - - uncategorized
date: 2019-04-17 22:27:14
---

思想：以redis中数据的有无来操作点赞和取消点赞的重复操作

首先在mongodb中添加一条测试数据（模拟用户id为\_16，当前点赞个数为0）

![](http://www.zby123.club/wp-content/uploads/2019/04/65FRX04Y5K2XX2X7QGVR1.png)

上代码

Service

```
//点赞
    public void updateLikes(String id){
        Query query = new Query();
        Criteria criteria = Criteria.where("_id").is(id);
        query.addCriteria(criteria) ;
        Update update = new Update();
        update.inc("likes",1);
        mongoTemplate.updateFirst( query,update,"Project_comment" )   ;
    }
    //取消点赞
    public void delUpdateLikes(String id){
        Query query = new Query();
        Criteria criteria = Criteria.where("_id").is(id);
        query.addCriteria(criteria) ;
        Update update = new Update();
        update.inc("likes",-1);
        mongoTemplate.updateFirst( query,update,"Project_comment" )   ;
    }
```

Controller

```
 //点赞
    @PutMapping("likes/{id}")
    public Result updateLikes(@PathVariable String id){
        String userId="9527";//假定用户userId是9527
        //key:likes_用户id_文章id
        //value: 1 （1表示已经点过赞）
        if(redisTemplate.opsForValue().get("likes_"+userId+"_"+id) !=null){
            commentService.delUpdateLikes(id);
            redisTemplate.opsForValue().set("likes_"+userId+"_"+id,null);
            return new Result(true,StatusCode.OK,"取消点赞成功",null);
        }
        commentService.updateLikes(id);
        redisTemplate.opsForValue().set("likes_"+userId+"_"+id,"1");
        return new Result(true,StatusCode.OK,"点赞成功",null) ;
    }
```

![](http://www.zby123.club/wp-content/uploads/2019/04/1.png)

首次执行

![](http://www.zby123.club/wp-content/uploads/2019/04/3.png)

再次点击

![](http://www.zby123.club/wp-content/uploads/2019/04/2.png)

查看mongodb数据