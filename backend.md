### Document Architecture of Alfresco System -- Backend
##
Đây là bài viết đầu tiên trong loạt bài viết về tìm hiểu Alfresco Content repository.

###### Cơ bản:
- Bài 1: [Tổng quan về Alfresco](http://acc.com/thomnv/alfresco/exp-1/)
- Bài 2: Kiến trúc source code của Alfressco Repository. (Bạn đang ở đây)
- Bài 3: Kiến trúc Source code của Application Content.
###### Nâng cao:
- Bài 4: Cách tạo một component trong Application Content.
- Bài 5: Cách tạo một web app như Application Content và cách deploy.
- Bài 6: Cách tạo module AMP và truy cập module.
- Bài 7: Cách tạo một Java Application truy cập Public Java API.
	
### 1. Target


- **a. Mục đích của tài liệu.**

	Mục đích của tài liệu này là để hiểu rõ hơn cấu trúc các services và APIs trong Alfresco content manage.
	Nếu bạn là một người mới tìm hiểu về Alfresco thì có thể bạn sẽ bị overload khi nhìn vào bộ source của Alfressco.
	Do đó tài liệu này sẽ mô tả rõ hơn về các bộ source cần thiết cần cho việc xây dựng một ứng dụng DMS (Document Manage System).
	
	Từ version 6.0 trở đi thì alfresco đã chia backend và frontend tách biệt (thay vì dùng Share như bản cũ). Backend là WebSciptAPI và RestFullAPI. Frontend sẽ là angular component.
	Trong tài liệu này chúng ta sẽ đi qua các project sau.

	- a. [Alfresco Repository (Community and Enterprise)](https://github.com/Alfresco/alfresco-repository "")
	- b. [Alfresco Remote API](https://github.com/Alfresco/alfresco-remote-api "Alfresco Remote API")
	- c. [Alfresco JS API](https://github.com/Alfresco/alfresco-js-api "Alfresco JS API")
	- d. [ADF framework](https://github.com/Alfresco/alfresco-ng2-components "ADF framework")
	- e. [Alfresco Example Content Application](https://github.com/Alfresco/alfresco-content-app "Content App")
### 2. Thiết kế tổng quan

2. Thiết kế tổng quan cho backend

	![ADF Architecture](https://docs.alfresco.com/sites/docs.alfresco.com/files/public/images/docs/defaultcommunity/adf-architecture.png)
	

	Dựa vào hình trên thấy Alfresco có tới 2 serivces là Alfresco Content Services (ACS) và Alfresco Process Service (APS).
	Trong bài viết này chúng ta chỉ quan tâm đến ACS và không quan tâm đến (APS)
	Chúng ta sẽ đi tìm các thành phần trong hình vẽ.
	
	##### 1.1 Phía Server
	Phía server có 2 phần là ReST API và Content Repository.
	Rest API nằm trong source code **Alfresco Remote API**, Còn Content Respository nằm trong source code **Alfresco Repository** .
	Rest API cung cấp tập API liên quan đến content. Còn Content Repository sẽ bao gồm các services.
	Có rất nhiều khái niệm API trong Alfresco Content Repository nhưng chúng ta chỉ giới hạn quan tâm đến Remote API. API cần thiết cho ADF components. Và bài viết này tập trung cho việc c

	##### 1.2 Phía Client
	Phía client được định nghĩa với Application Development Framework (ADF).ADF bao gồm các thành phần ADF components, Services nằm trong source code **ADF framework**. Để giao tiếp với RestAPI của thì Alfresco dựng lên một tập lớp nữa, đó là lớp Alfresco Javascript API. Lớp này có trách nhiệm tương tác với RestAPI của server site. Đồng thời layer này cũng tạo ra các entity rất hữu ích chứa response để dùng lại trong các services.

### 3. Content repository
- Tổng hợp các services
	
	Đa phần các services sẽ nằm trong package **org.alfresco.cmr.xxxxx**. Tất cả services được sẽ được định nghĩa trong cùng một class là **ServiceRegistry**. Class này có nhiệm vụ trả về các Services khi gọi hàm get services.

	- `ContentService`
	- `MimetypeService`
	- `SearchService`
	- `VersionService`
	- `LockService`
	- `JobLockService`
	- `DictionaryService`
	- `CopyService`
	- `CheckOutCheckInService`
	- `CategoryService`
	- `ActionService`
	- `PermissionService`
	- `AuthorityService`
	- `FileFolderService`
	- `ScriptService`
	- `WorkflowService`
	- `SiteService`
	- `AttributeService`
	- `TaggingService`
	- `RenditionService`
	- `RatingService`
	- `NodeLocatorService`


- Cấu tạo các services

	Chúng ta sẽ lấy một API để xem xét cho việc xây dựng một API cụ thể.
	Cách chọn API để tìm hiểu về cách tổ chức services thì ta chọn API nào không liên quan đến node và task để có thể run với không có tham số nào. Do đó chúng ta sẽ chọn TaggingServices là API đơn giản nhất.
	Repository Content được viết trên nền Spring 3 Framework. Các file được config với XML và phần enterprise được dấu đi trong source code.

	- **Tagging Service on Remote API**

		Như chúng ta đã biết Remote API là RestAPI cho phép truy cập tới content repository.
	Các API được miêu tả rõ ở **[REST API Explorer](https://api-explorer.alfresco.com/api-explorer/)**
	

	- **Tagging Service on Content Repository**

		Đây là API đơn giản nhất mà chúng ta có thể dễ dàng đọc code của nó.

		**Các class liên quan:**
		- org.alfresco.service.cmr.tagging.TaggingService - Service interface
		- org.alfresco.repo.tagging.TaggingServiceImpl - Implement service interface
		- public-services-context.xml - Define bean for registry service
		- application-context-core.xml - Define all core context
		- application-context.xml - context of application
		- org.alfresco.repo.service.ServiceDescriptorRegistry - Factory bean use for create service bean.
		- tagging-services-context.xml - Định nghĩa tag services.

		**Các Annotation liên quan:**
		- @AlfrescoPublicApi - Định nghĩa một API. Nếu API nào được đánh dấu là PublicAPI thì chúng ta không nên update code.
		- @NotAuditable - Chỉ dùng để đánh dấu các method/Class không Auditable
		- @Auditable - Chỉ định các method/class apply parameters. Dùng để filter khi call method ở 
3. Làm thế nào để tạo mới một service.
4. 
### 3. Remote API
1. Tổng hợp các API.
2. Cấu tạo của một API default.
3. Làm thế nào để tạo mới một API.
### 4. Tài liệu tham khảo:

1. [Alfresco REST API of the future BeeCon2016](http://beecon.buzz/2016/assets/data/files/20160401001/Alfresco_REST_API_of_the_future_BeeCon2016.pdf)

2. [https://community.alfresco.com/community/ecm/blog/2016/10/11/v1-rest-api-part-1-introduction](https://community.alfresco.com/community/ecm/blog/2016/10/11/v1-rest-api-part-1-introduction)

### 4. Thiếu sót & hạn chế.


