# 事务失效

```Java
@RequiredArgsConstructor
public class AlgorithmFactorCalculationModel implements Callable<Result> {
    static AlgorithmClient client = ;


    @Override
    @Transactional(value = "barActuatorIRDTransactionManager", rollbackFor = Exception.class)
    public Result call() throws Exception {
        Result result = new Result(1, "计算成功");
        JSONObject jsonObject = JSONObject.parseObject(pcs);
        String object = jsonObject.getString("object");
        String flag = jsonObject.getString("flag");
        String algorithm = jsonObject.getString("algorithm");
        String reCalObjCode = jsonObject.getString("reCalObjCode");

        try {
            irdMapper.deletetest();
            if(1/1==1){
                throw new RuntimeException("测试异常");
            }

        } catch (Exception e) {
//            result.setCode(-1);
//            result.setNote("计算失败:" + e.getMessage());
//            TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
            throw new RuntimeException("测试异常222");
        }
        return result;
    }
```

现象：@Transactional事务失效 是不是因为AlgorithmFactorCalculationModel类是通过new 的方式手动创建，调用方法，而不是spring创建的 

**ChatGPT** 

是的，你的观察是正确的。如果 **AlgorithmFactorCalculationModel** 类是通过 **new** 关键字手动创建的，而不是由 Spring 管理的 Bean，那么 Spring 的事务管理机制将无法生效。 

Spring 的事务管理是基于 AOP（Aspect-Oriented Programming）的，它通过在目标方法的前后添加事务处理逻辑来实现事务管理。但是，这仅在 Spring 管理的 Bean 上才会生效，因为 Spring 可以在实例化 Bean 时动态地创建代理对象，并在代理对象中添加事务逻辑。 

如果你手动使用 **new** 关键字创建了 **AlgorithmFactorCalculationModel** 的实例，那么 Spring 将无法对该实例进行代理，也就无法在方法调用前后添加事务逻辑。因此，即使你在 **AlgorithmFactorCalculationModel** 类的方法上添加了 **@Transactional** 注解，事务管理也不会生效。 

解决这个问题的方法是，让 Spring 来管理 **AlgorithmFactorCalculationModel** 类的实例。你可以通过将 **AlgorithmFactorCalculationModel** 声明为 Spring 的一个 Bean，并使用 Spring 的依赖注入机制来获取该 Bean 的实例，而不是手动创建它。这样，Spring 将能够为该 Bean 创建代理，并在调用其方法时添加事务逻辑，从而使事务管理生效。

[spring事务（注解 @Transactional ）失效场景_transactional注解不生效场景-CSDN博客](https://blog.csdn.net/yzx3105/article/details/130052944) 

1.4 (类本身) 未被spring管理 在我们平时开发过程中，有个细节很容易被忽略。即使用spring事务的前提是：对象要被spring管理，需要创建bean实例。 通常情况下，我们通过@Controller、@Service、@Component、@Repository等注解，可以自动实现bean实例化和依赖注入的功能。 

如下所示, 开发了一个Service类，但忘了加@Service注解，比如：

```Java
//@Service
public class UserService {

    @Transactional
    public void add(UserModel userModel) {
         saveData(userModel);
         updateData(userModel);
    }    
}
```

从上面的例子，我们可以看到UserService类没有加@Service注解，那么该类不会交给spring管理，所以它的add方法也不会生成事务。

# 手动事务

错误写法

```Java
    @Override
    public Result call() throws Exception {
        Result result = new Result(1, "计算成功");
        JSONObject jsonObject = JSONObject.parseObject(pcs);
        String object = jsonObject.getString("object");
        String flag = jsonObject.getString("flag");
        String algorithm = jsonObject.getString("algorithm");
        String reCalObjCode = jsonObject.getString("reCalObjCode");
        //手动事务
        DefaultTransactionDefinition def = new DefaultTransactionDefinition();
        def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
        TransactionStatus transaction = transactionManager.getTransaction(def);
        try {
            irdMapper.deletetest();
            //异常发生前就已经提交事务，当发生异常后并不会回滚
            transactionManager.commit(transaction);
            if(1/1==1){
                throw new RuntimeException("测试异常");
            }
        } catch (Exception e) {
            transactionManager.rollback(transaction);
            throw new RuntimeException("测试异常222");
        }
        return result;
    }
```

正确写法

```Java
    @Override
    public Result call() throws Exception {
        Result result = new Result(1, "计算成功");
        JSONObject jsonObject = JSONObject.parseObject(pcs);
        String object = jsonObject.getString("object");
        String flag = jsonObject.getString("flag");
        String algorithm = jsonObject.getString("algorithm");
        String reCalObjCode = jsonObject.getString("reCalObjCode");
        //手动事务
        DefaultTransactionDefinition def = new DefaultTransactionDefinition();
        def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);
        TransactionStatus transaction = transactionManager.getTransaction(def);
        try {
            irdMapper.deletetest();
            if(1/1==1){
                throw new RuntimeException("测试异常");
            }
            //将事务提交放到最后处理
            transactionManager.commit(transaction);
        } catch (Exception e) {
            transactionManager.rollback(transaction);
            throw new RuntimeException("测试异常222");
        }
        return result;
    }
```

# @ConfigurationProperties配置项被忽略

```Java
@Data
@Component
@ConfigurationProperties(prefix = "algorithm")
@SuppressWarnings("unchecked")
public class AlgorithmProperties {

    /**
     * 数据脚本配置
     */
    private List<DataConfig> dataConfigs;
}
algorithm:
  dataConfigs: # 数据配置
    - dataCode: coreData # 数据编码
      dataSource: iraaDs #数据源
      sql: "SELECT DT date, PD_CODE objCode, NAV nav FROM TIR_PD_NAV WHERE PD_CODE = ${pdCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
    - dataCode: IDX_ROR
      dataSource: iraaDs
      sql: "SELECT DT date, IDX_CODE objCode, ROR ror FROM TIR_IDX_ROR WHERE IDX_CODE = ${idxCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
    - dataCode: PD_NAV # 数据编码
      dataSource: iraaDs #数据源
      sql: "SELECT DT date, PD_CODE objCode, NAV nav FROM TIR_PD_NAV WHERE PD_CODE = ${pdCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
```

根据提供的配置文件，dataConfigs 中只有两个值而不是三个的原因是因为配置文件中的第三个元素被注释掉了。在 YAML 配置文件中，使用 # 符号可以注释掉一行或部分内容，被注释的内容会被忽略，不会被解析。 

在给定的配置文件中，第三个元素被注释掉了，即以下部分：

```Java
- dataCode: PD_NAV
  dataSource: iraaDs
  sql: "SELECT DT date, PD_CODE objCode, NAV nav FROM TIR_PD_NAV WHERE PD_CODE = ${pdCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
```

这样，dataConfigs 列表中只有两个元素，分别是：

1. 数据编码为 coreData，数据源为 iraaDs，SQL 查询语句为 "SELECT DT date, PD*CODE objCode, NAV nav FROM TIR*PD*NAV WHERE PD*CODE = ${pdCode} AND DT BETWEEN ${stratDate} AND ${endDt}"。
2. 数据编码为 IDX*ROR，数据源为 iraaDs，SQL 查询语句为 "SELECT DT date, IDX*CODE objCode, ROR ror FROM TIR*IDX*ROR WHERE IDX_CODE = ${idxCode} AND DT BETWEEN ${stratDate} AND ${endDt}"。

如果想要添加第三个元素，只需将注释取消，即去掉该行的 # 符号，使其成为配置文件的一部分。 

**修改后**

```YAML
dataConfigs: # 数据配置
  - dataCode: coreData # 数据编码
    dataSource: iraaDs # 数据源
    sql: "SELECT DT date, PD_CODE objCode, NAV nav FROM TIR_PD_NAV WHERE PD_CODE = ${pdCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
  - dataCode: IDX_ROR
    dataSource: iraaDs
    sql: "SELECT DT date, IDX_CODE objCode, ROR ror FROM TIR_IDX_ROR WHERE IDX_CODE = ${idxCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
  - dataCode: PD_NAV
    dataSource: iraaDs
    sql: "SELECT DT date, PD_CODE objCode, NAV nav FROM TIR_PD_NAV WHERE PD_CODE = ${pdCode} AND DT BETWEEN ${stratDate} AND ${endDt}"
```

# static代码块读取配置文件值为null

```Java
@Component
public class AlgorithmFactorCalculationProcessor implements IDynamicJobProcessor {


    @Override
    public String getName() {
        return "algorithmFactorCalculation";
    }


    @Autowired
    private IRDMapper irdMapper;

    @Autowired
    private AlgorithmScheduleConfig algorithmScheduleConfig;

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Resource(name="barActuatorIRDTransactionManager")
    private DataSourceTransactionManager transactionManager;

    @Autowired
    private CallBarApiMapper callBarApiMapper;

    private static String address;

    private static String port;
    @Value("${algorithm2.server.address:localhost}")
    public void setAddress(String param) {
        address = param;
    }

    @Value("${algorithm2.server.port:8001}")
    public void setPort(String param) {
        port = param;
    }

    static AlgorithmClient client = null;
    static {
        try {
            // 客户端初始化包含两个必须步骤
            // 1. 添加服务器地址
            // 初始化逻辑建议在系统中只执行一次，可以将其放在静态代码块或者单例对象中
            // 其中核心关键点是addHTTPServer每次调用，都会创建一个okHttp的连接池
            // 如果每次请求，都添加addHTTPServer，将直接导致连接池资源浪费，内存泄露

            // 获取客户端单例对象
            client = AlgorithmClient.Instance.singleton();
            // 添加服务器地址
            client.addHTTPServer(address, Integer.parseInt(port));

        } catch (ClientException e) {
            e.printStackTrace();

            // 如果初始化异常，销毁对象，回收资源
            if (client != null) {
                client.destory();
            }
        }
    }
}
```

静态代码块执行时，address和port的值为null **原因：**

> 静态代码块在类加载时执行，而 **setAddress** 和 **setPort** 方法则是在 Spring 完成依赖注入后才被调用。这意味着，在静态代码块执行时，**address** 和 **port** 属性还未被赋值，因此它们仍然保持初始值 **null**。  解决这个问题的一种方法是将静态代码块的逻辑移到 **setAddress** 和 **setPort** 方法内部，或者在调用 **execute** 方法时进行初始化。这样可以确保在需要使用 **address** 和 **port** 的时候，它们已经被正确地赋值了。

非静态代码块（实例初始化块）在每次创建bean实例时都会执行一次。这意味着每次通过Spring容器获取一个bean实例时，非静态代码块都会执行一次，而不是只执行一次。 静态代码块在类加载时执行，并且只执行一次。而非静态代码块则在每次创建实例时执行。 例如，如果你有一个Spring管理的单例bean，那么在整个应用生命周期内，非静态代码块只会执行一次，因为该单例bean只会被创建一次。但是，如果你有一个原型（prototype）作用域的bean，那么每次通过Spring容器获取该bean实例时，非静态代码块都会执行一次。

# 实例化与初始化

在Spring中，bean的实例化和初始化是两个不同的过程：

1. **实例化（Instantiation）**：实例化是指创建bean对象的过程。当Spring容器启动时，它会根据配置文件或者注解等元数据信息，通过反射机制或者其他方式创建bean的实例。在实例化过程中，Spring会调用bean的构造方法来创建对象。
2. **初始化（Initialization）**：初始化是指对bean实例进行配置、赋值、依赖注入等操作的过程。在实例化完成后，Spring容器会对bean进行一系列的初始化工作，例如执行**@PostConstruct**注解的方法、调用**InitializingBean**接口的**afterPropertiesSet()**方法、执行自定义的初始化方法等。在初始化过程中，Spring会将属性值注入到bean中，并且进行一些额外的设置和操作，确保bean对象已经准备好被使用。

在整个过程中，Spring容器负责管理bean的生命周期，包括实例化、初始化、使用和销毁。通过IoC（控制反转）和DI（依赖注入），Spring容器可以动态地管理bean，并且能够确保它们被正确地实例化和初始化。

# 怎么让一个方法在初始化之后执行

在Spring中，可以使用 **@PostConstruct** 注解来标记一个方法，在Bean初始化完成后执行该方法。这个方法将在依赖注入完成后、任何 **InitializingBean** 的 **afterPropertiesSet()** 方法之后调用。

```Java
import javax.annotation.PostConstruct;

@Component
public class MyBean {

    @PostConstruct
    public void init() {
        // 在初始化完成后执行的逻辑
    }
}
```

在这个示例中，**init()** 方法将在 **MyBean** 被实例化并初始化后执行。 另外，你也可以实现 **InitializingBean** 接口，并重写 **afterPropertiesSet()** 方法来实现相同的效果：

```Java
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Component;

@Component
public class MyBean implements InitializingBean {

    @Override
    public void afterPropertiesSet() throws Exception {
        // 在初始化完成后执行的逻辑
    }
}
```

这两种方法都能够让你在Bean初始化之后执行特定的逻辑。