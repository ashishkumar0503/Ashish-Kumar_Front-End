# Ashish-Kumar_Front-End

## Steeleye Limited Frontend Developers (React.JS)  Assignment

### Code

``` js

import React, { useState, useEffect, memo } from 'react';
import PropTypes from 'prop-types';

// Single List Item
const WrappedSingleListItem = ({
  index,
  isSelected,
  onClickHandler,
  text,
}) => {
  return (
    <li
      style={{ backgroundColor: isSelected ? 'green' : 'red'}}
      onClick={onClickHandler(index)}
    >
      {text}
    </li>
  );
};

WrappedSingleListItem.propTypes = {
  index: PropTypes.number,
  isSelected: PropTypes.bool,
  onClickHandler: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

const SingleListItem = memo(WrappedSingleListItem);

// List Component
const WrappedListComponent = ({
  items,
}) => {
  const [setSelectedIndex, selectedIndex] = useState();

  useEffect(() => {
    setSelectedIndex(null);
  }, [items]);

  const handleClick = index => {
    setSelectedIndex(index);
  };

  return (
    <ul style={{ textAlign: 'left' }}>
      {items.map((item, index) => (
        <SingleListItem
          onClickHandler={() => handleClick(index)}
          text={item.text}
          index={index}
          isSelected={selectedIndex}
        />
      ))}
    </ul>
  )
};

WrappedListComponent.propTypes = {
  items: PropTypes.array(PropTypes.shapeOf({
    text: PropTypes.string.isRequired,
  })),
};

WrappedListComponent.defaultProps = {
  items: null,
};

const List = memo(WrappedListComponent);

export default List;
```

Based on the code above answer the following queries:
1. Explain what the simple ``` List ``` component does.

`Ans:` 

``` 
The List component is a simple React functional component that renders an unordered list of items, where each item is represented as 
a SingleListItem component.

The SingleListItem component is a memoized functional component that receives four props:

i. index: A number representing the index of the item in the array of items.
ii. isSelected: A boolean indicating whether the item is currently selected or not.
iii. onClickHandler: A function that handles the click event on the list item.
iv. text: A string representing the text content of the list item.

The SingleListItem component renders an li element with a background color based on the isSelected prop, and adds an onClick 
handler that calls the onClickHandler prop with the index prop as an argument.

The List component receives a prop called items, which is an array of objects containing a text property that is a string. The 
component initializes a state variable selectedIndex with the useState hook, which is initially set to null.

The useEffect hook is used to reset the selectedIndex state variable to null whenever the items prop changes.

The List component maps each item in the items array to a SingleListItem component and passes the following props:

i. onClickHandler: A function that sets the selectedIndex state variable to the index of the clicked item.
ii. text: The text property of the current item.
iii. index: The index of the current item in the items array.
iv. isSelected: A boolean indicating whether the current item is selected or not, which is determined by comparing its index to the 
selectedIndex state variable.

The List component then renders an unordered list (ul) element that contains all the SingleListItem components. 
```

2. What problems / warnings are there with code?

`Ans:`
```
There are a few problems/warnings with the code:

1. items prop type is not defined correctly: 
The PropTypes for the items prop in WrappedListComponent is not defined correctly. 
Instead of PropTypes.array(PropTypes.shapeOf({ text: PropTypes.string.isRequired })), 
it should be PropTypes.arrayOf(PropTypes.shape({ text: PropTypes.string.isRequired })).

2. setSelectedIndex should be useState(null) instead of just useState(): 
In the WrappedListComponent, the setSelectedIndex hook should be initialized with a default value of null like this 
const [selectedIndex, setSelectedIndex] = useState(null);.

3. onClickHandler prop in SingleListItem component is not defined correctly: 
The onClickHandler prop in the SingleListItem component should be defined as a function that calls onClickHandler 
with the index as an argument like this: onClick={() => onClickHandler(index)}. This will ensure that the onClickHandler 
is called correctly when the list item is clicked.

4. isSelected prop in SingleListItem component should be a boolean: 
The isSelected prop in the SingleListItem component should be a boolean, but it is being passed the selectedIndex state variable, 
which is a number. Instead, it should be passed a boolean value like this: isSelected={selectedIndex === index}.

5. Missing key prop in the SingleListItem component: 
When mapping over an array of items to create a list of components, each component should have a unique key prop that helps React 
identify the component and optimize rendering. In this code, the SingleListItem component is missing the key prop, which should be set 
to the item's unique identifier (e.g. item.id).

6. Redundant memoization of SingleListItem and WrappedListComponent: Since neither SingleListItem nor WrappedListComponent have any 
internal state, memoization is not necessary and can be removed.
```

3. Please fix, optimize, and/or modify the component as much as you think is necessary.

`Ans:`

``` js
// The modified code of List Component is:

import React, { useState, useEffect } from 'react';
import PropTypes from 'prop-types';

// Single List Item
const SingleListItem = ({ index, isSelected, onClickHandler, text }) => {
  const handleClick = () => {
    onClickHandler(index);
  };

  return (
    <li
      style={{ backgroundColor: isSelected ? 'green' : 'red' }}
      onClick={handleClick}
    >
      {text}
    </li>
  );
};

SingleListItem.propTypes = {
  index: PropTypes.number.isRequired,
  isSelected: PropTypes.bool.isRequired,
  onClickHandler: PropTypes.func.isRequired,
  text: PropTypes.string.isRequired,
};

// List Component
const List = ({ items }) => {
  const [selectedIndex, setSelectedIndex] = useState(null);

  useEffect(() => {
    setSelectedIndex(null);
  }, [items]);

  const handleClick = (index) => {
    setSelectedIndex(index);
  };

  return (
    <ul style={{ textAlign: 'left' }}>
      {items.map(({ id, text }, index) => (
        <SingleListItem
          key={id}
          index={index}
          isSelected={selectedIndex === index}
          onClickHandler={handleClick}
          text={text}
        />
      ))}
    </ul>
  );
};

List.propTypes = {
  items: PropTypes.arrayOf(
    PropTypes.shape({
      id: PropTypes.oneOfType([PropTypes.number, PropTypes.string]),
      text: PropTypes.string.isRequired,
    })
  ).isRequired,
};

export default List;
```

```
In this modified version of the List component:

1. The items prop type is defined correctly using PropTypes.arrayOf and PropTypes.shape: 
By defining the prop type using PropTypes.arrayOf and PropTypes.shape, we can provide more detailed information about the structure of the items array. 
This makes it easier to spot errors and ensures that the component is used correctly.

2. The setSelectedIndex hook is initialized with a default value of null:
By providing a default value for setSelectedIndex, we can avoid potential bugs that could arise if the value is undefined or null. This also makes the 
code more explicit and easier to read.

3. The onClickHandler prop in the SingleListItem component is defined as a function that calls onClickHandler with the index as 
an argument using a separate handleClick function:
By defining a separate handleClick function and passing it to onClickHandler, we can avoid unnecessary re-renders of the SingleListItem component due 
to changes in the onClickHandler function reference. This optimization can improve performance, especially if the List component contains a large 
number of items.

4. The isSelected prop in the SingleListItem component is now correctly defined as a boolean expression using selectedIndex === index:
By using selectedIndex === index to define the isSelected prop in the SingleListItem component, we ensure that only the selected item is highlighted. 
In the original code, the isSelected prop was always truthy, which would cause all items to be highlighted.

5. The key prop has been added to the SingleListItem component, set to the item's id:
By adding a unique key to each SingleListItem component, we help React identify which items have changed, been added, or been removed. 
This allows React to update the DOM more efficiently, improving performance.

6. Memoization has been removed since it's not necessary in this case and could actually cause more harm than good in terms of performance:
In the original code, the SingleListItem and ListComponent components were memoized using React.memo(). However, memoization should only be used when 
necessary, as it can sometimes lead to subtle bugs. In this case, memoization is not necessary, as the component is already optimized using other techniques.
```

```
However, the following optimization we can also consider:

1. UseCallback: We can also consider using the useCallback hook to memoize the handleClick function in the List component. This would 
prevent unnecessary re-renders of the SingleListItem component due to changes in the handleClick function reference. However, this 
optimization may not be necessary in most cases.
```



