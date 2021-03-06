# RainORM
> 基于TabooLib的Kotlin ORM框架
> 
> 暂时只支持MySQL
> 
> 设计参考了Android Jetpack Room
> 
> ~~提供接口, 可供外部实现~~
> 
> 个人能力原因，暂不提供外部实现接口，只提供TabooLib平台实现

## 完工,已过基本测试
算是对自己Kotlin开发能力的一次实践

不会写框架的开发者不是好开发者

## 设计
~~~kotlin
data class ExampleEntity(
    // PrimaryKey 不用写option
    @PrimaryKey(autoGenerate = true)
    @Column(name = "id", type = ColumnTypeSQL.INT)
    val id: Int? = null,
    @Column(name = "type", type = ColumnTypeSQL.TEXT, options = [ColumnOptionSQL.NOTNULL])
    val type: String,
    @Column(name = "user", type = ColumnTypeSQL.TEXT, options = [ColumnOptionSQL.NOTNULL])
    val user: String,
    @Column(name = "user", type = ColumnTypeSQL.TEXT, def = "null")
    val data: String?
)

interface ExampleDAO : IDao<ExampleEntity> {
    @Query("SELECT * FROM {table}")
    fun queryAll(): List<ExampleEntity>

    @Insert
    fun insert(entity: ExampleEntity)
    
    @Query("DELETE FROM {table}")
    fun delete()
    
    @Query("SELECT WHERE id > {id} FROM {table}")
    fun querySome(id: Int): List<ExampleEntity>
    
    // 使用TabooLib DSL语句控制
    @DSL
    fun selectUser(name: String): ExampleEntity? {
        return table.workspace(datasource) {
            select { where { "user" eq name } }
        }.firstOrNull {
            adaptResultSet()
        }
    }
}

object AppDatabase {
    /**
     * 也可通过ORMBuilder#buildFromConf(ConfigurationSection, String)直接构建
     */
    private val builder by lazy {
        ORMBuilder.newBuilder()
            .host("localhost")
            .port(3306)
            .user("root")
            .password("root")
            .database("database")
            .buildHost()
    } 
    
    
    val exampleTableDao by lazy {
        // 表名 DAO类
        builder.build("exampleTable" ,ExampleDAO::class.java)
    }
}
~~~