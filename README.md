mybatis-pagable
===============

mybatis 分页拦截器，使用方法和思路：

1、分页对象采用spring-data中的Page和Pageable，其中Pageable作为分页请求，Page为返回的分页对象。

2、拦截器会对方法中的参数进行分析，如果发现Pageable类型的参数，则认为是分页请求，将请求封装为Page对象返回。

Mapper接口方法写法

```java
public interface SysUserDao {	
	Page<SysUser> findPageByParams(Map<String, Object> params, Pageable pageRequest);
}
```

Mapper.xml 写法(和普通查询一样)

```xml
<select id="findPageByParams" resultType="SysUser">
    <include refid="findSql" />
    
    <where>
        <if test="param1.groupId != null">
            u.group_id = #{param1.groupId}
        </if>
    </where>
    
    ORDER BY u.add_time
</select>
```

service 调用写法(注意返回类型和包装请求对象为Pageable)
```java
@Service
public class SysUserServiceImpl implements SysUserService {
	
	@Resource
	private SysUserDao sysUserDao;

	@Override
	public Page<SysUser> findPage(Map<String, Object> params, int pageNo, int pageSize) {
	
		Pageable pageRequest = new PageRequest(pageNo, pageSize);
		return this.sysUserDao.findPageByParams(params, pageRequest);
	}	
}
```

注意，由于项目中采用 Page类和Pageable类来源于 spring-data-commons，请添加对spring-data-commons的依赖。
