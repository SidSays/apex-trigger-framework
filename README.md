# Apex Trigger, Handler and Helper framework
## Simple, Scalable & Maintainable apex framework

The absolute buzzkill is the mention of "an apex trigger framework. "Either brace yourself or grab the remote and dive into the wild world of your favorite TV show!

> This is not an original concept neither I claim it to be. Feel free to add/modify to your own needs and relate/refer to any other sources.

## Description
In my 14+ years implementing salesforce apex (as of 2022) for highly complex multi orgs to simple orgs, this framework has helped me most in terms of time saving, ease to educate developers, keeping regression impact minimum yet empowering future scalability and ease of deployments.

> For me, the pros far outweigh the cons.

## Why?
- You lead a team which is a mixed bag of differently skilled developers, admins and architects
- Team changes due to attrition, vendor switch, In house, so its difficult to follow documentation trail
- Everyone has been following some complex framework which isn't fully understood by everyone
- There needs to be something simple, efficient, scalable where new functionlity is always kept separate to avoid regression
- Identifying and isolating bugs is easy to fix and deploy when there is a helper class instead of a HUGE MAMMOTH Handler class
- The org has multiple business units with different release dates and different admins/developers and product owners
- Deployments can be short and efficient because they're deploying specific helper classes and their test classes

## How?
- A switch (Custom Metadata) to turn the trigger for an object ON or OFF, e.g. during data migrations
- One trigger per object best practice
- Trigger invokes a Handler Class (implements an interface)
- Handler class can invoke methods from smaller Helper classes for each Business Unit and specific feature/use case, or
- Handler class may invoke methods from a common Helper class where common and generic code is organized

### Step 1: Create a custom metadata (switch) that enables turning a trigger ON/OFF for every object
- Trigger_Switch__mdt	
- The Is_trigger_On__c custom checkbox field
  
<img width="1164" alt="image" src="https://github.com/SidSays/apex-trigger-handler-helper-framework/assets/25760138/97bad0e2-eb9a-4d8e-9cf0-38def9205a7a">

- Create records for the trigger on every object

<img width="992" alt="image" src="https://github.com/SidSays/apex-trigger-handler-helper-framework/assets/25760138/b3e6a4b5-c8af-4513-8c3c-e12819f8c9a0">


### Step 2: Create a TriggerHandler Interface (i.e. Apex Class)

> NOTE: Only one is needed in every org. (Not per object). This is because we will have multiple Handlers in the org for each SObject. All those Handlers will refer to this interface.

```
public interface TriggerHandler {
    void beforeInsert(List<SObject> newRecords);
    void beforeUpdate(List<SObject> oldRecords, List<SObject> newRecords, Map<ID, SObject> oldRecordMap, Map<ID, SObject> newRecordMap);
    void beforeDelete(List<SObject> oldRecords, Map<ID, SObject> oldRecordMap);
    void afterInsert(List<SObject> newRecords, Map<ID, SObject> newRecordMap);
    void afterUpdate(List<SObject> oldRecords, List<SObject> newRecords, Map<ID, SObject> oldRecordMap, Map<ID, SObject> newRecordMap);
    void afterDelete(List<SObject> oldRecords, Map<ID, SObject> oldRecordMap);
    void afterUndelete(List<SObject> newRecords, Map<ID, SObject> newRecordMap);
}
```

### Step 2: Create a Trigger Handler Class for each SObject (Case in this example)

> NOTE: For every context, each business unit can call its own use case specific helper class or call the common helper class for that object. This is what is the most beautiful and manageable part of using this framework. You can have multiple projects with different developers, architects and release dates work out of a single org and minimize regression impacts

```
public without sharing class CaseTriggerHandler implements TriggerHandler {
    private boolean triggerIsExecuting;
    private integer triggerSize;
    public CaseTriggerHandler(boolean triggerIsExecuting, integer triggerSize) {
        this.triggerIsExecuting = triggerIsExecuting;
        this.triggerSize = triggerSize;
    }
    public void beforeInsert(List<Case> newCases) {
    /* BUSINESS UNIT 1 */
    //commonCaseHelperClass.methodName(newCases);
    //specifiCaseHelperClass.methodName(newCases);
    /* BUSINESS UNIT 2 */
    /* BUSINESS UNIT 3 */
    }
    public void beforeUpdate(List<Case> oldCases, List<Case> newCases, Map<ID,Case> oldCaseMap, Map<ID, Case> newCaseMap) {
    /* BUSINESS UNIT 1 */
    /* BUSINESS UNIT 2 */    
    /* BUSINESS UNIT 3 */ 
    }
    public void beforeDelete(List<Case> oldCases, Map<ID, Case> oldCaseMap) {
    /* BUSINESS UNIT 1 */
    /* BUSINESS UNIT 2 */    
    /* BUSINESS UNIT 3 */ 
    }
    public void afterInsert(List<Case> newCases, Map<ID, Case> newCaseMap) {
    /* BUSINESS UNIT 1 */
    /* BUSINESS UNIT 2 */    
    /* BUSINESS UNIT 3 */ 
    }
    public void afterUpdate(List<Case> oldCases, List<Case> newCases, Map<ID, Case> oldCaseMap, Map<ID, Case> newCaseMap) {
    /* BUSINESS UNIT 1 */
    /* BUSINESS UNIT 2 */    
    /* BUSINESS UNIT 3 */ 
    }
    public void afterDelete(List<Case> oldCases, Map<ID, Case> oldCaseMap) {
    /* BUSINESS UNIT 1 */
    /* BUSINESS UNIT 2 */    
    /* BUSINESS UNIT 3 */ 
    }
    public void afterUndelete(List<Case> newCases, Map<ID, Case> newCaseMap) {
    /* BUSINESS UNIT 1 */
    /* BUSINESS UNIT 2 */    
    /* BUSINESS UNIT 3 */ 
    }
}
```

### Step 1: Trigger that invokes the Handler

> NOTE: Trigger checks switch is on or off for the object using custom metadata record

```
trigger CaseTrigger on Case (before insert, before update, before delete, after insert, after update, after delete, after undelete) {
    if (Trigger_Switch__mdt.getInstance('CaseTrigger')?.Is_trigger_On__c == true) {
        TriggerHandler handler = new CaseTriggerHandler(Trigger.isExecuting, Trigger.size);
        switch on Trigger.operationType {
            when BEFORE_INSERT {
                 handler.beforeInsert(Trigger.new);
            } 
            when BEFORE_UPDATE {
                 handler.beforeUpdate(Trigger.old, Trigger.new, Trigger.oldMap, Trigger.newMap);
            }
            when BEFORE_DELETE {
                 handler.beforeDelete(Trigger.old, Trigger.oldMap);
            }
            when AFTER_INSERT {
                 handler.afterInsert(Trigger.new, Trigger.newMap);
            }
            when AFTER_UPDATE {
                 handler.afterUpdate(Trigger.old, Trigger.new, Trigger.oldMap, Trigger.newMap);
            }
            when AFTER_DELETE {
                 handler.afterDelete(Trigger.old, Trigger.oldMap);
            } 
            when AFTER_UNDELETE {
                 handler.afterUndelete(Trigger.new, Trigger.newMap);
            }
        }
    }
}
```

