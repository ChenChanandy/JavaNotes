# SSM框架常用注解

- **@Component**

  创建类对象,相当于配置`<bean/>`

- **@Service**

  与@Component 功能相同，写在 ServiceImpl 类上

- **@Repository**

  与@Component 功能相同，写在数据访问层类上

- **@Controlle**

  与@Component 功能相同，写在控制器类上

- **@Resource**

  java 中的注解，默认按照 byName 注入,如果没有名称对象,按照 byType 注入；建议把对象名称和 spring 容器中对象名相同

- **@Autowired**

  spring 的注解，默认按照 byType 注入

- **@Reference**

  dubbo中的注解

  ````java
  @Reference
  private TbItemDubboService tbItemDubboServiceImpl;
  ````

- **@Value()**

  获取 properties 文件中内容

  ````java
  @Value("${redis.item.key}")
  private String itemkey;
  ````

  .properties文件中：`redis.item.key = item:`

- **@PathVariable**

  获取URL中的值

  ````java
  @RequestMapping("item/{id}.html")
  public String showItemDetails(@PathVariable long id)
  ````

- **@RequestParam**

  可以设置默认值

  ````java
  @RequestMapping("content/category/list")
  @ResponseBody
  public List<EasyUiTree> showCategory(@RequestParam(defaultValue ="0") long id){
  	return tbContentCategoryServiceImpl.showCategory(id);
  }
  ````

- **@ResponseBody**

  将controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到response对象的body区，通常用来返回JSON数据或者是XML数据，需要注意的呢，在使用此注解之后不会再走试图处理器，而是直接将数据写入到输入流中，他的效果等同于通过response对象输出指定格式的数据。**页面不会跳转**

- **@RequestMapping**

  设置请求编码格式

  ````java
  @RequestMapping(value = "solr/init",produces = "text/html;charset=utf-8")
  ````

- **@RequestHeader**

  获取请求头中的内容

  ````java
  @RequestMapping("user/showLogin")
  public String showLogin(@RequestHeader(value="Referer",defaultValue = "") String url,Model model,String interurl) {
      if(interurl!=null&&!interurl.equals("")) {
          model.addAttribute("redirect", interurl);
      }else if(!url.equals("")) {
          model.addAttribute("redirect", url);
      }		
      return "login";
  }
  ````

- **AOP相关注解**

  @Pointcut() 定义切点

  @Aspect() 定义切面类

  @Before() 前置通知

  @After 后置通知

  @AfterReturning 后置通知,必须切点正确执行

  @AfterThrowing 异常通知

  @Arround 环绕通知

