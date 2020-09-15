##MybatisPlus
####一.相关总结
1.关于MybatisPlus内置分页Page助手
    
    （1）.简单的分页查询（单表），无需任何条件，对查询数据直接进行分页处理操作：
         controller层直接调用service层，在业务层进行直接返回分页对象page(page)。
    代码如下：
            @PostMapping("/queryByPage")
            @ApiOperation("分页")
            public Result<Page<VideoEntity>> queryByPage(@RequestBody Page<VideoEntity> page) {
                return Result.success(videoService.queryByPage(page));
            }
            
            Page<VideoEntity> queryByPage(Page<VideoEntity> page);
            
            @Override
            public Page<VideoEntity> queryByPage(Page<VideoEntity> page) {
                return page(page);
            }
            // 用@RequestBody 作为接收前端传递分页参数，所有参数放置body体中。
    
    ========================================================================================
    （2）.如果需要对单表继续添加条件，可以在serviceImpl中创建一个Wrappers对象，在page(page,conditions)
    中继续添加。
    类似如下：
            LambdaQueryWrapper<VideoEntity> wrapper = Wrappers.lambdaQuery(VideoEntity.class)
                            .eq(StrUtil.isNotEmpty(type), VideoEntity::getType, type)
                            .eq(VideoEntity::getStatus, STATUS_NORMAL)
                            .eq(VideoEntity::getAuditStatus, AUDIT_STATUS_PASS);
                    return page(page.getPage(), wrapper);
#### 2.条件查询
~~~.java
   MyBatisPlus通常会封装三个实体类：
   DTO层的返回给前端的最终封装结果；
   Entity层的与数据库相对应的实体对象；
   Query层的需要进行查询的条件封装实体。

分页查询可能需要的需求：
（1）.多条件分页查询（针对于多表关联查询而后封装查询结果）
    将需要查询的所有条件进行封装，具体可封装为一个Query对象，存放所有的查询条件，并在query对象进行
    private Page<EvaluateEntity> page;
    
     


（2）.简单的条件分页查询（针对单表若干条件查询）
       

 
（3）.简单的分页查询（纯粹的单表查询而后分页处理）
    
