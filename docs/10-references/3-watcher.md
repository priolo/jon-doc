---
id: "watcher"
title: 'Watcher'
sidebar_label: 'Watcher'
sidebar_position: 3
---


### addWatch
Inserts a WATCHER into JON
```ts 
addWatch( watcher:Watcher )
```
- [watcher](#watcher)  



### removeWatch
Removes a listener from JON
if `actionName` is `null` delete all listener of STORE
if `callback` is `null` delete all listener of `actionName`
```ts 
removeWatch( watcher:Watcher )
```
- [watcher](#watcher)  


---

### Watcher
Identifies a `callback` called when a specific STORE `action/mutator` is executed
```ts
interface Watcher {
	store: Store,
	actionName: string,
	callback: WatchCallback,
}
```
- store  
STORE instance to be observed
- actionName  
name of the 'action' or 'mutator' to be observed
- callback  
Function executed when 'action' or 'mutator' are called
`(msg: WatchMsg) => void`

### WatchMsg
Data of an event that occurred in a STORE. 
```ts
interface WatchMsg {
	type: EVENTS_TYPES,
	store: Store,
	key: string,
	payload: any,
	result: any,
	subcall: boolean
}
```
- type
