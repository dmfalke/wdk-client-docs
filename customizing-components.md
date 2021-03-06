---
title: Customizing Wdk Components
---

## Background

WDK React Components are available via the global JavaScript object `Wdk.Components`, or ES named imports (`import { Record } from 'wdk-client/Components'`). Each Component has a static method `wrapComponent` that can be used to modify the default Component.

The `wrapComponent` method takes a `ComponentFactory` function as its sole argument. The `ComponentFactory` should return a React Component that will be used instead of the original Component. The `ComponentFactory` will be called with the original Component as its sole argument.

```typescript
interface ComponentFactory<Props> {
  (TargetComponent: React.ComponentType<Props>): React.ComponentType<Props>
} 
```

The Component that is returned by the `ComponentFactory` will be passed the same props as the original Component. Keep in mind that the Component passed to `ComponentFactory` can be any valid React Component type (class, function, etc).

This API allows for many different types of modifications, including:

* Wholesale replacement
* [Programming with advise](https://en.wikipedia.org/wiki/Advice_(programming))
* Injecting/modifying props
* etc.

The underlying idea is that the components defined in WDK declare an interface (via Props) that consumers can rely upon. Eventually, WDK will generate documentation based on Props and comments. This can then be used as a guide for expectations when wrapping components.

## Examples

### Replace Component

```jsx
Wdk.Components.Record.wrapComponent(function(Record) {
  return function CustomRecord(props) {
    return (
      <CoolChartingComponent data={props.tables.CoolTable.values} />
    )
  };
});
```


### Before Component

```jsx
Wdk.Components.Record.wrapComponent(function(Record) {
  return function BeforeRecord(props) {
    return (
      <div>
        <CustomComponent/>
        <Record {...props}/>
      </div>
    );
  };
});
```


### After Component

```jsx
Wdk.Components.Record.wrapComponent(function(Record) {
  return function AfterRecord(props) {
    return (
      <div>
        <Record {...props}/>
        <CustomComponent/>
      </div>
    );
  };
});
```


### Around Component

```jsx
Wdk.Components.Record.wrapComponent(function(Record) {
  return function AroundRecord(props) {
    return (
      <RecordWrapper>
        <Record {...props}/>
      </RecordWrapper>
    );
  };
});
```


### Modify Props

_This is probably an anti pattern and WDK will hopefully provide better ways to do this in the Model XML._

```jsx
Wdk.Components.Record.wrapComponent(function(Record) {
  return function PropsModifier (props) {
    let newProps = Object.assign({}, props, {
      overriddenProp: newValue
    });
    return (
      <Record {...newProps}/>
    );
  };
});
```
