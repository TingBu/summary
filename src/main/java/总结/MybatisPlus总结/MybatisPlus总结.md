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
#### 2.Wrapper内置条件构造器

#### 3.条件查询
``````
MyBatisPlus通常会封装三个实体类：
   DTO层的返回给前端的最终封装结果；
   Entity层的与数据库相对应的实体对象；
   Query层的需要进行查询的条件封装实体。

分页查询可能需要的需求：
（1）.多条件分页查询（针对于多表关联查询而后封装查询结果）
    将需要查询的所有条件进行封装，具体可封装为一个Query对象，存放所有的查询条件，并在query对象进行
    private Page<EvaluateEntity> page;  
    示例如下：
    【Controller层：
       @PostMapping("/queryByConditions")
       @ApiOperation("评价管理多条件查询")
       public Result<Page<EvaluateDTO>> query(@Validated @RequestBody EvaluateQuery evaluateQuery) {
           return Result.success(evaluateService.queryByConditions(evaluateQuery));
       }
    】

    【Service层：
     interface:
       /**
        * @param evaluateQuery
        * @return
        */
       Page<EvaluateDTO> queryByConditions(EvaluateQuery evaluateQuery);
        
     interfaceImplement:
        @Override
        public Page<EvaluateDTO> queryByConditions(EvaluateQuery evaluateQuery) {
            return baseMapper.query(evaluateQuery.getPage(), evaluateQuery);
        }
    】

   【Mapper层：
        /**
         * @param page
         * @param evaluateQuery
         * @return
         */
        Page<EvaluateDTO> query(Page<EvaluateEntity> page, @Param("evaluateQuery") EvaluateQuery evaluateQuery);
    】

    【DTO实体封装：
    @Data
    @ApiModel("评价DTO")
    @EqualsAndHashCode(callSuper = false)
    @ExcelTarget("EvaluateDTO")
    public class EvaluateDTO extends EvaluateEntity implements Serializable{
    
        private static final long serialVersionUID = -2665309621889551107L;
    
        @ExcelIgnore
        @ApiModelProperty("评价星级")
        private Integer starLevel;
    
        @ExcelIgnore
        @ApiModelProperty("点赞数")
        private Integer praiseCount;
    
        @ExcelIgnore
        @ApiModelProperty("头像")
        private String avatar;
    
    
        @ApiModelProperty("姓名")
        @Excel(name = "姓名",orderNum = "1")
        private String name;
        ...
        }
    】

    【Entity封装实体：
     @Data
     @ApiModel("培训评价")
     @TableName("tra_evaluate")
     @EqualsAndHashCode(callSuper = true)
     @ExcelTarget("EvaluateEntity")
     public class EvaluateEntity extends BaseLogicEntity implements Serializable {
         private static final long serialVersionUID = 1L;
     
         public static final int RETURN_VISIT_LIMIT_STAR = 3;
     
         @ApiModelProperty(value = "学员科目ID", required = true)
         @NotNull
         @ExcelIgnore
         private Long studentSubjectId;
     
         @ApiModelProperty(value = "内容", required = true)
         @NotEmpty
         @Excel(name = "学员评价",orderNum = "10",width = 40.0)
         private String content;
     
         @ApiModelProperty(value = "服务质量", required = true)
         @NotNull
         @Range(min = 1, max = 5)
         @Excel(name = "服务质量",orderNum = "5")
         private Integer serviceQuality;
     
         @ApiModelProperty(value = "培训质量", required = true)
         @NotNull
         @Range(min = 1, max = 5)
         @Excel(name = "培训质量",orderNum = "6")
         private Integer trainQuality;
     
         @ApiModelProperty(value = "车内整洁", required = true)
         @NotNull
         @Range(min = 1, max = 5)
         @Excel(name = "车内整洁",orderNum = "7")
         private Integer carClean;
     
         @ApiModelProperty(value = "驾校服务")
         @Range(min = 1, max = 5)
         @Excel(name = "驾校服务",orderNum = "9")
         private Integer schoolService;
     
         @ApiModelProperty(value = "吃拿卡要(0-否，1-是)", required = true)
         @NotNull
         @Range(min = 0, max = 1)
         @Excel(name = "吃拿卡要",orderNum = "8")
         private Integer bribeTaking;
     
         @ApiModelProperty("打赏金额")
         @Null
         @Excel(name = "打赏金额",orderNum = "11")
         private BigDecimal tip;
     
         @ApiModelProperty("打赏是否已支付")
         private Integer isPaid;
     
         @ApiModelProperty("备注")
         @Excel(name = "备注",orderNum = "14",width = 30.0)
         private String remark;
     
         @ApiModelProperty("是否解决完毕(0-否，1-是)")
         @Null
         @Excel(name = "是否解决完毕",orderNum = "13")
         private Integer isResolved;
        }
    】

    【Query封装实体：
    @Data
    @ApiModel("评价管理多条件查询")
    public class EvaluateQuery {
    
        @NotNull
        private Page<EvaluateEntity> page;
    
        @ApiModelProperty("学员姓名")
        private String name;
    
        @ApiModelProperty("学员手机号码")
        private String phone;
    
        @ApiModelProperty("学员身份证号")
        private String idCard;
    
        @ApiModelProperty("学员科目")
        private Integer subject;
    
        @ApiModelProperty("起始时间")
        private Date startTime;
    
        @ApiModelProperty("结束时间")
        private Date endTime;
        }
    】

（2）.简单的条件分页查询（针对单表若干条件查询）
    查询的是单表，但有可能需要查询条件，且统一（1）的请求访问分页方式，封装一个Query查询条件，因尽可能避免单表查询时编写SQL，使用MybatisPlus
    自带的分页助手以及查询，具体代码如下：
    【Entity实体：
      @Data
      @ApiModel("消息通知")
      @TableName("sys_message")
      @EqualsAndHashCode(callSuper = true)
      public class MessageEntity extends BaseLogicEntity {
          private static final long serialVersionUID = 1L;
      
          @ApiModelProperty("消息类型 SYS_MESSAGE-系统消息,SCHOOL_MESSAGE-驾校消息")
          private MessageType type;
      
          @ApiModelProperty("发送用户ID")
          private Long sender;
      
          @ApiModelProperty("发送时间")
          private LocalDate sendTime;
      
          @ApiModelProperty("接收用户ID")
          private Long receiver;
      
          @ApiModelProperty("是否已读")
          private Integer isRead;
      
          @ApiModelProperty("读取时间")
          private Date readTime;
      
          @ApiModelProperty("消息标题")
          private String title;
      
          @ApiModelProperty("消息内容")
          private String content;
      
          @ApiModelProperty("消息数据")
          private String data;
      }
    】
    
    【Query查询条件实体：
       @Data
       @ApiModel("消息通知查询条件")
       public class MessageQuery {
       
           @NotNull
           @ApiModelProperty("分页")
           Page<MessageEntity> page;
       
       }
    】
    
    【Controller层：
       @PostMapping("/queryByUser")
       @ApiOperation("消息通知分页")
       public Result<Page<MessageEntity>> queryByUser(@RequestBody MessageQuery query){
           return Result.success(messageService.queryByUser(query));
       }
    】
    
    【Service层：
     interface：
       /**
        * @param query
        * @return
        */
       Page<MessageEntity> queryByUser(MessageQuery query);
    
      interfaceImplement:
       @Override
       public Page<MessageEntity> queryByUser(MessageQuery query) {
           LambdaQueryWrapper<MessageEntity> wrapper = Wrappers.lambdaQuery();
           wrapper.eq(MessageEntity::getReceiver,UserUtils.getUserIdNotNull())
                   .orderByDesc(MessageEntity::getId);
           return page(query.getPage(),wrapper);
       }
    】
       

 
（3）.简单的分页查询（纯粹的单表查询而后分页处理）
    查询的是单表，分页查询的只是封装的实体Entity。具体代码参考如下：
    【Entity实体（对应数据库封表中的数据）
      @Data
      @ApiModel("视频")
      @TableName("sys_video")
      @EqualsAndHashCode(callSuper = false)
      public class VideoEntity extends BaseLogicEntity {
          private static final long serialVersionUID = 4938108359289395555L;
      
          @ApiModelProperty("类型")
          private String type;
      
          @ApiModelProperty("标题")
          private String title;
      
          @ApiModelProperty("描述")
          private String description;
      
          @ApiModelProperty("上传者")
          private Long uploaderId;
      
          @ApiModelProperty("上传者昵称")
          private String uploaderNickName;
      
          @ApiModelProperty("上传时间")
          private Date uploadTime;
      
          @ApiModelProperty("视频地址")
          private String url;
      
          @ApiModelProperty("文件ID")
          private Long ossId;
      
          @ApiModelProperty("封面图片")
          private String coverUrl;
      
          @ApiModelProperty("审核状态")
          private Integer auditStatus;
      
          @ApiModelProperty("状态")
          private Integer status;
      
          @ApiModelProperty("对象id")
          private Long targetId;
      
          @ApiModelProperty("时长(秒)")
          private Double duration;
      
          @ApiModelProperty("浏览次数")
          private Integer viewCount;
      }  
    】

    【Conroller层：
       @PostMapping("/queryByPage")
       @ApiOperation("分页")
       public Result<Page<VideoEntiyt>> queryByPage(@RequestBody Page<VideoEntiyt> page) {
           return Result.success(videoService.queryByPage(page));
       }
    】

    【Service层：
     interface:
        /**
         * @param page
         * @return
         */
        Page<VideoDTO> queryByPage(Page<VideoEntiyt> page);

     interfaceImplement:
        @Override
        public Page<VideoDTO> queryByPage(Page<VideoEntiyt> page) {
            return page(page);
        }
    】