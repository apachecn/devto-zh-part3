# 编辑锁定机制 SALESFORCE 对象详细信息

> 原文：<https://dev.to/girikon/editing-locking-mechanism-salesforce-object-detail-2261>

**简介**

当在 Salesforce 中处理任何对象以及我们想要编辑的记录时，我们可以这样做，但是当另一个用户也在同一时间在同一 Salesforce 组织中编辑同一记录时，则两个用户都将编辑以前保存的详细信息。下面是问题的细节，首先保存记录的用户将更改细节，然后编辑同一记录的另一个用户仍在编辑先前保存的记录。当第二个用户不知道第一个记录已经更改，并且他们正要更改一个旧记录时，这会导致问题。

作为一名 Salesforce 顾问，我让 QA 团队拒绝了一项功能，因为我是对同一 Salesforce 对象进行多项更改的第一、第二甚至第三个用户。 **[Girikon 的 Salesforce Consulting services](https://www.girikon.com.au/salesforce-consultant/)**团队由数百名 sales force 顾问组成，多达 10+人可能同时从事同一个项目，这有时会使所有顾问很难知道其他顾问正在编辑哪些对象。

**解决方案比你想象的要简单……**

现在我们来考虑一下解决方案。我们可以为其他用户锁定记录。这是一个很棒的特性，因此第二个和第三个用户必须刷新才能获得新的详细信息。

每当两个或更多用户打开 Salesforce 对象记录进行编辑时，触发第一个保存事件的用户具有优先权，并且能够保存记录。当第二个用户保存更改时，用户将被锁定，记录将不会被保存。要解除锁定，第二个用户需要刷新并重新打开该特定记录的编辑窗口。现在，将向用户呈现由第一个用户保存的新细节。我在下面开发了一个实用的指南来帮助解决这个问题。

**遵循下面的逐步解决方案…**

*   首先，应该通过 visual force page 或 lightning 在屏幕上显示对象(即您要编辑的对象)的记录。 [![lightning](img/6f0d4a081f4727cabcd75458caed57e9.png)](https://www.girikon.com/wp-content/uploads/2019/04/lightning.png) -现在选择您想要编辑其详细信息的记录。 [![record](img/a6b8644e2b0948da6fe298548b58b859.png)](https://www.girikon.com/wp-content/uploads/2019/04/record.png) -如果两个或两个以上的用户同时编辑同一条记录，那么优先点击保存按钮的用户将只能保存该记录。
*   如果其他用户单击保存按钮，他们将被定向到一个新页面，该页面将显示刷新页面的消息。[![new page](img/4e247463aa68f6a200c62ba104d596df.png)](https://www.girikon.com/wp-content/uploads/2019/04/new-page.png)——为了创建这个你必须在你的 VF 页面中添加下面提到的视觉力代码。

```
<apex:page controller="lockingMachenismForAnyObject" sidebar="false"> <apex:form > <apex:pageBlock > <apex:pageBlockSection> <apex:inputText value="{!searchName}" label="ENTER THE NAME TO EDIT"/> </apex:pageBlockSection> <apex:commandButton value="search" action="{!search}"> </apex:commandButton> <apex:pageBlockTable value="{!ReturningList}" var="v"> <apex:column headerValue="USER NAME" title="NAME"> <apex:outputField value="{!v.name}"/> </apex:column <apex:column headerValue="USER PHONE NUMBER" title="PHONE NO"> <apex:outputField value="{!v.phone}"/> </apex:column < apex:column headerValue="USER FAX NUMBER" title="FAX NO"> <apex:outputField value="{!v.fax}"/> < /apex:column> < apex:column headerValue="USER ID" title="ID"> < apex:outputField value="{!v.id}" /> </apex:column> <apex:inlineEditSupport event="ondblClick"> < showOnEdit="saveButton,cancelButton"/> </apex:pageBlockTable> <apex:pageBlockButtons> <apex:commandButton value="Save" action="{!save}" id="saveButton"/> < apex:commandButton value="Cancel" action="{!cancel}" id="cancelButton"/> </apex:pageBlockButtons> < apex:pageBlockSection > <apex:inputText value="{!newName}" label="Enter New NAME"/> </apex:pageBlockSection> <apex:pageBlockSection> <apex:inputText value="{!newPhone}" label="Enter New PHONE"/> </apex:pageBlockSection> <apex:pageBlockSection > < apex:inputText value="{!newFax}" label="Enter New FAX"/> </apex:pageBlockSectio > < /apex:pageBlock> </apex:form> 
```

*   对于上面的 VF 代码，下面一个是 apex 代码。

```
public without sharing class lockingMachenismForAnyObject { //Describing all the variables. public string searchName{get;set;} public string idOfSearchedName{get;set;} public string newName{get;set;} public string newPhone{get;set;} public string newFax{get;set;} // Fetching the list of Account records. public List<account> ob= new List<account>([select name,phone,fax, id from account]);
    //initialising Method.
    public list<account> getReturningList() { 
        return ob;
    }
    //initialising Method.
    public void search()
    {
        //Modifying the above list according to the name of the record which we want to edit.
        ob= new List<account>([select id,name,phone,fax from account where name = : searchName limit 1]);
        idOfSearchedName = ob[0].id;
        Account[] accountObject = [SELECT Id FROM Account where name = : searchName];
        //Checking the locking condition.
        if(Approval.isLocked(accountObject[0].id))
        {
            Approval.unLock(accountObject);
        }
    }
    //initialising Method.
    public pageReference save()
    {
        if(Approval.isLocked(idOfSearchedName))
        {
            //Sending the user to the locked page if the locking condition is satisfied.
            pageReference pr=Name of the connected page to display.
            return pr;               
        }
        else
        {
            //Else saving the record and locking the same record for the other user.
            List<account> ob=new List<account>();
            Account updateObject=[select id,name,phone,fax from account where id = : idOfSearchedName];
            updateObject.name=newName;
            updateObject.phone=newPhone;
            updateObject.fax=newFax;
            ob.add(updateObject);
            update ob;
            Account[] accountObject = [SELECT Id FROM Account where name = : searchName];
            Approval.lock(accountObject);
            return null;
        }
    }
    //initialising Method.
    public pageReference cancel()
    {
        pageReference pr=page.pramodSirVF2;
        return pr;
    }
}
</account></account></account></account></account></account> 
```

由编辑锁定机制 SALESFORCE 对象细节协助的帖子 [SALESFORCE CONSULTANTS 首先出现在](https://www.girikon.com/blog/salesforce-consultants-assisted-by-the-editing-locking-mechanism-salesforce-object-detail/)[sales force Consulting Company | United States | Phoenix，Arizona](https://www.girikon.com/) 上。