========================================================================
    ASP.Net APPLICATION : CSASPNETAjaxScriptControl 项目概述
========================================================================

/////////////////////////////////////////////////////////////////////////////
用法:

CSASPNETAjaxScriptControl示例演示了如何创建一个ASP.Net Ajax ScriptControl, 
一个允许用户在日历安排任务的日程表.


/////////////////////////////////////////////////////////////////////////////
先决条件:

ASP.NET AJAX Control Toolkit
http://www.codeplex.com/AjaxControlToolkit


/////////////////////////////////////////////////////////////////////////////
代码逻辑:

Microsoft ASP.Net Ajax Extensions使你可以扩展ASP.Net Web Application的可用性
来创建丰富的客户体验. 
我们可以通过使用ScriptControl或ExtenderControl来建立富客户端行为或web控件.
ExtenderControl与ScriptControl的区别在于
Extender用来建立在已有的被称为行为的目标控件上的客户端脚本, 
而ScriptControl是包含富客户端功能的虚拟web 控件.
举例来说, 当我们想建立一个弹出在已有面板上的ModalPopup,隐藏显示功能是客户端脚本应用程序,
可以将它建为ExtenderControl. 

但是, 相对于scriptcontrol之类的控件而言, TabContainer是一个全新的包括可编程
客户端脚本的web控件, 因此我们可以把它建立为一个ScriptControl.

1. ASP.NET页面开发人员建立有web丰富的客户体验的web服务端控件,可以使用
脚本控件.(在Visual Studio中File->New->Project->Web->ASP.Net Ajax Server Control
创建ASP.Net Ajax服务端控件项目) 
脚本控件是继承了System.Web.UI命名空间中的ScriptControl虚拟类的web服务器控件. 
脚本控件用来建立富客户端可用性的web服务器控件. 

public class Schedule : ScriptControl

2. 你必须在脚本控件中实现以下两个ScriptControl虚拟类的方法.

        public IEnumerable<ScriptDescriptor> GetScriptDescriptors()
        {
            ScriptControlDescriptor descriptor = new ScriptControlDescriptor
("PainControls.Schedule", this.ClientID);
            descriptor.AddElementProperty("toolContainer", ToolContainer.ClientID);

            descriptor.AddElementProperty("dateTimePicker", 
DateTimePicker.ClientID);
            descriptor.AddElementProperty("calendarContainer", 
CalendarContainer.ClientID);
            descriptor.AddProperty("calendarCellContentCssClass", 
CalendarCellContentCssClass);
            if (string.IsNullOrEmpty(ServicePath))
                throw new Exception("Please set ServicePath property.");
            descriptor.AddProperty("servicePath", ServicePath);
            if (string.IsNullOrEmpty(UpdateServiceMethod))
                throw new Exception("Please set UpdateServiceMethod property.");
            descriptor.AddProperty("updateServiceMethod", UpdateServiceMethod);
            if (string.IsNullOrEmpty(DeleteServiceMethod))
                throw new Exception("Please set DeleteServiceMethod property.");
            descriptor.AddProperty("deleteServiceMethod", DeleteServiceMethod);

            

            List<string> aa = new List<string>();
            for (int i = 0; i < DropPanelClientIDCollection.Count; i++)
            {
                string a = DropPanelClientIDCollection[i].ClientID;
                aa.Add(a);
            }
         
            descriptor.AddProperty("dropPanelClientIDCollection", aa);
           
            descriptor.AddProperty("dateTimeFieldName", DateTimeFieldName);
            descriptor.AddProperty("titleFieldName", TitleFieldName);
            descriptor.AddProperty("descriptionFieldName", DescriptionFieldName);
            yield return descriptor;
        }

        // Generate the script reference
        public IEnumerable<ScriptReference> GetScriptReferences()
        {

            yield return new ScriptReference(Page.ClientScript.GetWebResourceUrl
(this.GetType(), "PainControls.Schedule.Schedule.js"));

        }

3. 在预渲染阶段嵌入CSS引用.

private void RenderCssReference()
{
string cssUrl = Page.ClientScript.GetWebResourceUrl(this.GetType(), 
"PainControls. Schedule. Schedule.css");
HtmlLink link = new HtmlLink();
link.Href = cssUrl;
link.Attributes.Add("type", "text/css");
link.Attributes.Add("rel", "stylesheet");
Page.Header.Controls.Add(link);
}

4. 设定所有资源(包括图片, CSS文件和js文件)
以"Embedded Resource"(属性"Build Action")嵌入于此脚本控件.

5. 如果需要的话,本脚本控件可以派生自IScriptControl接口和一个服务器控件
以替代脚本控件.

控件可以派生自IScriptControl接口和一个服务器控件如果你希望使他继承自
一个服务器控件而非脚本控件. 
示例: 

public class Schedule : DataBoundControl, IScriptControl,INamingContainer 

在本场合,本脚本控件应该派生自IScriptControl接口和一个服务器控件.
同时, 我们还有两个步骤要完成:

1) 复写OnPreRender方法. 在预渲染阶段注册web控件为脚本控件.
ScriptManager manager = ScriptManager.GetCurrent(this.Page);
if (manager == null)
{
  throw new InvalidOperationException("A ScriptManager is required on the page.");
}
manager.RegisterScriptControl<Schedule>(this);
2) 复写Render方法. 注册已定义的脚本描述符. 
ScriptManager.GetCurrent(this.Page).RegisterScriptDescriptors(this);

6. 剩下的工作在客户端. 首先注册客户端命名空间.

Type.registerNamespace("PainControls");

7. 建立客户端类.

PainControls.Schedule = function(element) 
{
}
PainControls.Schedule.prototype = {
}

8. 注册类继承"Sys.UI.Control".

PainControls.Schedule.registerClass('PainControls. Schedule¡¯, Sys.UI.Control);

9. 在构造方法中调用基类方法

PainControls. Schedule.initializeBase(this, [element]);

10. 实现Initialize和Dispose方法.
在类原型中建立"initialize"和"dispose"方法. 
Initialize方法在行为的实例被创建时调用. 
使用这个方法来设定属性默认值,创建函数代理, 
添加代理到事件句柄. Dispose方法行为的实例不再被页面使用并移除时被调用. 
使用这个方法来释放不再被行为使用的资源,比如DOM事件句柄.

initialize: function() {
PainControls. Schedule.callBaseMethod(this, 'initialize');
},
dispose: function() {
PainControls. Schedule.callBaseMethod(this, 'dispose');
}

11.定义属性的Get和Set方法.
每一个脚本控件GetScriptDescriptors()方法的脚本描述符对象中定义的属性
必须有关联的客户端访问器. 客户属性访问器定义为客户类原型中的
get_<:property>和set_<:property>方法.

    get_titleFieldName: function() {
        return this._titleFieldName;
    },

    set_titleFieldName: function(val) {
        if (this._titleFieldName !== val) {
            this._titleFieldName = val;
            this.raisePropertyChanged('titleFieldName');
        }
    },

12. 定义DOM元素的事件句柄
1) 在构造函数中定义句柄:
this._element_focusHandler = null;
2) 在初始化方法中关联句柄到DOM元素事件:
this._element_focusHandler = Function.createDelegate(this, this._element_onfocus);
3) 在初始化方法中增加句柄:
$addHandler(this.get_element(), 'focus', this._element_focusHandler)
4) 建立事件的回调方法:
_element_onfocus:function(){
}

13. 定义行为的事件句柄
每一个脚本控件GetScriptDescriptors()方法的脚本描述符对象中定义的事件
必须有关联的客户端访问器. 客户事件访问器定义为客户类原型中的
add_<:event>和remove_<:event> 方法.Raise<:event>定义为事件触发器.

add_showing: function(handler) {
this.get_events().addHandler("showing", handler);
},
remove_showing: function(handler) {
this.get_events().removeHandler("showing", handler);
},
raiseShowing: function(eventArgs) {
var handler = this.get_events().getHandler('showing');
if (handler) {
handler(this, eventArgs);
}
},

14. 在页面中使用Schedule脚本控件.

1) 关于Schedule控件.

1. 日程绑定在DataSource控件上.你可以在左边日历上任选一日, 
它将从DataSource获取数据, 在右侧容器中显示关联任务.
2. 每个任务将被显示在DropPanel上. 你可以拖放到另一个单元格(天).它将被自动更新.
3. 当你用鼠标移过droppanel, 将会显示CloseButton用来移除任务.
4. 当你用鼠标移过日程单元格, 他被设计为展示更多的任务.
5. 左侧的日历, 有任务的日期会被高亮.
6. 因为这是个Ajax ScriptControl, 可以随意使用css式样.
7. 在本项目中Schedule使用其他两个脚本控件ButtonList 和 DropPanel .
8. 你需要建立和绑定web服务文件在这个控件上异步完成更新和删除函数.

2) 需要被扩展的可用性

这里只是一个简单的日程. 在下个版本中下列函数将被扩展或实现.
1. 除了"Month"显示模式, 还要"Day"显示模式.
2. 目前, 你可以创建DetailView来插入一个新任务, 因为Schedule 
绑定在DataSource. 
   日后,将扩展为内部插入函数.

3) 如何使用PainControl Schedule:

1.在页面中注册程序集.

<%@ Register TagPrefix="PainControls" Assembly="PainControls" 
Namespace="PainControls" %>

2. 在页面中使用
<asp:ScriptManager ID="ScriptManager1" runat="server" />
<Pain:Schedule ID="Schedule" runat="server" AutoPostBack="true" 
DataSourceID="SqlDataSource1" 
KeyField="num" 
DateTimeFieldName="date_time" 
TitleFieldName="title" 
DescriptionFieldName="description" 
ServicePath="ScheduleWebService.asmx" 
UpdateServiceMethod="UpdateWebService" 
DeleteServiceMethod="DeleteWebService" />
<asp:SqlDataSource ID="SqlDataSource1" runat="server"
ConnectionString="<%$ ConnectionStrings:DatabaseConnectionString %>"
SelectCommand="SELECT * FROM [schedule]"></asp:SqlDataSource>

3. 属性
DataSourceID ---- 创建一个DataSource绑定到Schedule.
KeyField ---- DataSource主键的字段名.
必须创建的三个强制性的字段:
DateTimeFieldName ---- 任务日期关联字段名
TitleFieldName ---- 任务标题关联字段名
DescriptionFieldName ---- 任务描述关联字段名
ServicePath ---- 如果你使用AjaxControlToolkit, 你必须熟悉这个属性. 
表示web服务文件的路径.
UpdateServiceMethod ---- 执行更新函数的web方法名
DeleteServiceMethod ---- 执行删除函数的web方法名

4. 建立web服务绑定实现更新和删除函数

下列示例代码片段是更新和删除函数的web方法.
在更新函数的web方法中:
"key" 将被更新的主键.
"updateFieldName" 将被更新的字段名.
"updateValue" 关联到"updateFieldName"将被更新的值.

在删除函数的web方法中,
只需要主键"key"就能删除.
[WebMethod]
[System.Web.Script.Services.ScriptMethod]
public void UpdateWebService(string key, string updateFieldName, string updateValue)
{
string constr = 
(string)ConfigurationManager.ConnectionStrings["DatabaseConnectionString"].ConnectionString;
string sql = 
"update schedule set " + updateFieldName + "='" + updateValue+"' where num="+key;
SqlConnection connection = new SqlConnection(constr);
SqlCommand sdc = new SqlCommand(sql, connection);
sdc.CommandType = CommandType.Text;
try
{
connection.Open();
sdc.ExecuteScalar();
}
catch (SqlException SQLexc)
{
throw new Exception(SQLexc.Message);
}
finally
{
connection.Close();
}
System.Threading.Thread.Sleep(2000);

}

[WebMethod]
[System.Web.Script.Services.ScriptMethod]
public void DeleteWebService(string key)
{

string constr = 
(string)ConfigurationManager.ConnectionStrings["DatabaseConnectionString"].ConnectionString;
string sql = "delete from schedule where num=" + key;
SqlConnection connection = new SqlConnection(constr);
SqlCommand sdc = new SqlCommand(sql, connection);
sdc.CommandType = CommandType.Text;
try
{
connection.Open();
sdc.ExecuteScalar();
}
catch (SqlException SQLexc)
{
throw new Exception(SQLexc.Message);
}
finally
{
connection.Close();
}
System.Threading.Thread.Sleep(2000);
}



