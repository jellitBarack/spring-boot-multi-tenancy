# Spring Boot 2 Multi-Tenant Example

This application demonstrates multi-tenancy in a Spring-Boot 2 app 
using a discriminator field with Hibernate.

Approach:

* Uses hibernate filter to limit the query results based on tenant.
* Uses hibernate interceptors to enforce tenant details during creating/updating entities.
* Uses Spring AOP (AspectJ) to set the filter parameters.


Explanation: 

* Each request goes thru a custom servlet filter which checks for `X-TenantID` http header and set's it in the ThreadLocal variable using `TenantContext` class. If http header is not present in request, it'll be rejected.
* Controller routes the request to Service class and the Spring AOP (`EmployeeServiceAspect` class) intercepts the service call and set's the hibernate tenant filter.
* All the service method has to be annotated with `@Transactional` for `EmployeeServiceAspect` to work.
* Above method works only for read queries, for write queries, we have to use hibernate interceptors.
* Custom Entity interceptor (using `EmptyInterceptor`) class which sets the tenantId value during the save/delete/flush-dirty entity events.
* Entity class should implement `TenantSupport` interface for the Entity interceptor to work. 

Refer:
* https://github.com/ramsrib/multi-tenant-app-demo
* https://hibernate.atlassian.net/browse/HHH-6054
* https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#multitenacy
* https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#mapping-column-filter
* https://docs.jboss.org/hibernate/orm/5.2/userguide/html_single/Hibernate_User_Guide.html#events