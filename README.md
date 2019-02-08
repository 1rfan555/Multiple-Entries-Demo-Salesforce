# Multiple-Entries-Demo-Salesforce
Save multiple records in Salesforce org with remove link. Screenshots are provided below.


First we need one object(Custom/Standard) and some fields on that object.
Then We will require visualforce page/lightning component.
Controller to implement its functionality.
Lets have a code:

    Custom Object: Multiple_Entry__c
    
    
    Controller code: MultipleEntriesDemoController.apxc
    
    public with sharing class MultipleEntriesDemoController {
    
    //save
     public PageReference SaveMultiple() {
    system.debug('controller save method is calling-->');
     MultipleEntriesDemoHelper.save(EntryList);
    return null;
    }
    
    public List<WrapperEntriesList> EntryList {get;set;}
    public Integer rowToRemove {get;set;}
    
    //add
    public void addNewRowToEntryList(){
     EntryList = MultipleEntriesDemoHelper.AddnewRow(EntryList);
    }
    //constructor
    public MultipleEntriesDemoController(){
    EntryList = new List<WrapperEntriesList>();
    addNewRowToEntryList();
    }
    //remove
    public void removeRowFromEntry(){
    EntryList = MultipleEntriesDemoHelper.removeRow(rowToRemove, EntryList);
   
    }
    
    //wrapperList
    public class WrapperEntriesList{
        public Integer index {get;set;}
        public Multiple_Entry__c record {get;set;}
    } 

    }
	
	
	
	
	
	Helper Code: MultipleEntriesDemoHelper.apxc
	
	
	public class MultipleEntriesDemoHelper {
 	public static List<MultipleEntriesDemoController.WrapperEntriesList> 
     AddnewRow(List<MultipleEntriesDemoController.WrapperEntriesList> EntryList){
        MultipleEntriesDemoController.WrapperEntriesList newRecord = new MultipleEntriesDemoController.WrapperEntriesList();
        Multiple_Entry__c newEntryRecord = new Multiple_Entry__c();        
        newRecord.record = newEntryRecord;
        newRecord.index = EntryList.size();
        EntryList.add(newRecord);
        return EntryList;
    	}
    
     public static List<MultipleEntriesDemoController.WrapperEntriesList>
         removeRow(Integer rowToRemove, List<MultipleEntriesDemoController.WrapperEntriesList> EntryList){
        EntryList.remove(rowToRemove);
        return EntryList;
    }

    public static void save(List<MultipleEntriesDemoController.WrapperEntriesList> EntryList) {
        system.debug('==EntryList==>'+EntryList.size());
        List<Multiple_Entry__c> RecordsToBeInserted = new List<Multiple_Entry__c>();
        if(EntryList !=null && !EntryList.isEmpty()){
            for(MultipleEntriesDemoController.WrapperEntriesList eachRecord : EntryList ){
                Multiple_Entry__c entryTemp = eachRecord.record;
                RecordsToBeInserted.add(entryTemp);
               
            }
            system.debug('==RecordsToBeInserted==>'+RecordsToBeInserted.size());
            upsert RecordsToBeInserted;
        }
    }
    
    
	}
	
	
	
	Visualforce page: MultipleEntriesDemo.vfp
	
	<apex:page controller="MultipleEntriesDemoController">
    <apex:slds />
	<apex:form id="theForm">
 	<apex:pageblock id="thePB" title="Insert Multiple Entries">
  	<apex:pageblockButtons >
   	<apex:commandButton value="Save" action="{!SaveMultiple}"/>
  
  	</apex:pageblockButtons>

  	<apex:outputPanel id="EntryHead">
  	<apex:variable value="{!0}" var="rowNum"/>  
   	<apex:pageBlockSection columns="1" title="Multiple Entries" id="thePbs" collapsible="False"> 
   
     <apex:pageBlockTable value="{!EntryList}" var="eachRecord"> 
      
      <apex:column headerValue="Action">
        <apex:commandLink value="Remove" style="color:red" action="{!removeRowFromEntry}" rendered="{!rowNum > 0}" rerender="EntryHead" 		immediate="true" >
             <apex:param value="{!rowNum}" name="rowToRemove" assignTo="{!rowToRemove}"/>
         </apex:commandLink>
         <apex:variable var="rowNum" value="{!rowNum + 1}"/>
      </apex:column>
      
      <apex:column headerValue="Name">
                            <apex:inputField value="{!eachRecord.record.Name}" required="true"/>
       </apex:column>
      
      <apex:column headerValue="Age">
                            <apex:inputField value="{!eachRecord.record.Age__c}" required="true"/>
       </apex:column>
       
       
       <apex:column headerValue="Salary">
                                <apex:inputfield value="{!eachRecord.record.Salary__c}" required="true"/>
        </apex:column>
         
         <apex:column headerValue="Currency">
                                <apex:inputfield value="{!eachRecord.record.CurrencyIsoCode}" required="true"/>
        </apex:column> 
    
    </apex:pageBlockTable>
   	</apex:pageBlockSection>
   	<apex:commandButton value="Add More" action="{!addNewRowToEntryList}" rerender="EntryHead" Status="status" immediate="true" />
   
  	</apex:outputPanel>

 	</apex:pageblock>
	</apex:form>
  
	</apex:page>

