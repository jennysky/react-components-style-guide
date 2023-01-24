<img style="margin-top: 32px" src="https://drive.google.com/uc?export=download&id=14ND2SUGlhYQcpqV7c12PzbpCqZMBBH-7"/>

# UI components Code Style Guide

UI components best practices with React and styled-components


# Introduction

This is meant to be a guide to help new developers understand
the best practices to implement components.



# Table of contents
- [Files Folders naming](#files-folders-naming)
- [Component definition](#component-definition)
    - [Structure](#structure)
    - [Named Export](#named-export)
    - [Forward Ref](#forward-ref)
- [Styled Components](#styled-components)
    - [ClassName](#ClassName)
    - [Naming](#naming)
    - [Props theme](#props-theme)
    - [Theme tokens](#theme-tokens)
    - [Export styled components](#export-styled-components)
    - [Styled components properties](#styled-components-properties)
- [Code standards](#code-standards)
    - [Destruct your props](#destruct-your-props)
    - [Code style](#code-style)
    - [Use explanatory variables](#use-explanatory-variables)
    - [Use intention revealing names](#use-searchable-names)
    - [One component per file](#one-component-per-file)
- [CSS Design Patterns](#css-design-patterns)
    - [The parent constrains the child](#the-parent-constrains-the-child)
    - [Components never leak margin](#components-never-leak-margin)
    - [The parent spaces the children](#the-parent-spaces-the-children)
  - [Components Design Patterns](#components-design-patterns)
    - [Use Controlled components](#use-controlled-components)
    - [Do not use redundant state](#do-not-use-redundant-state)
    - [API design](#api-design) 
    - [Composability](#composability)
    - [Typography](#typography)



# Files Folders naming

- Components folder - PascalCase
- Component file - PascalCase
- Utils files/folders - kebab-case

Example:

```
MyCard/
├── index.is
├── MyCard.tsx
├── BaseCard
├────── index.is
├────── BaseCard.tsx
└── my-card-utils.ts
```

[Back to top][table-of-contents]

# Component definition

### Structure
All components (presentation, containers or pages) should **always** be <br/>
defined as a directory, named with pascal casing.<br/> The main component file
should be `index.ts`

```
MyCard/
├── index.ts
├── MyCard.tsx
```

### Named export
Use named exports. Benefits of the Named export:
1. Explicit over implicit - Named exports are explicit, forcing the consumer to import with the names the original author intended and removing any ambiguity.
2. Refactoring actually works - Because named exports require you to use the name of the object, function or variable that was exported from a module, refactoring works across the board.
3. Codebase look-up - Because default exports can have any name applied to them, it's almost impossible to perform a look-up in your codebase, especially if a naming convention isn't put in place.
4. Better Tree Shaking - Instead of exporting a single bloated object with properties you may or may not need, named exports permit you to import individual pieces from a module, excluding unused code from the bundle during the build process.


Bad
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
`;

const Button = ({ children, className }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>

export default Button;
```

Good
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
`;

export const Button = ({ children, className }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>
```
<br/>

### Forward Ref
Building reusable components we want to enable users to attach ref to those components.<br />
The way to do it is to wrap components with React.forwardRef
https://reactjs.org/docs/forwarding-refs.html


Example
```jsx
import { forwardRef } from 'react';
import styled from 'styled-components';

const StyledButton = styled.button`
`;

export const Button = forwardRef(({ children, className }), ref) =>
  <StyledButton className={className} ref={ref}>
    {children}
  </StyledButton>



//Usage
const buttonRef = useRef(null);
render <Button ref={buttonRef}/>
```
<br/>


[Back to top][table-of-contents]

# Styled Components

### ClassName
Add className to the container of the component.<br/> 
in order to be able to override styles from the parent


Bad
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
`;

export const Button = ({ children}: { children: ReactNode}) =>
  <StyledButton>
    {children}
  </StyledButton>
```

Good
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
`;

export const Button = ({ children, className}: { children: ReactNode; className?: string }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>

Usage
const MyButton = styled(Button)`
   margin-right: 16px;
`;

<StyledButton/>

```
<br/>

Do not use string class names with styled components

Bad
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
   .contained {
      background: blue;
   }
   
   .outlined {
      background: white;
      border: 1px solid red;
   }
`;

export const Button = ({ children, className, variant = "contained" }) =>
  <StyledButton className={variant + className}>
    {children}
  </StyledButton>
```

Good
```jsx
import styled, { css } from 'styled-components';

const StyledButton = styled.button<{variant: string}>`
   ${props => {
     switch (props.variant) {
       case "outlined": {
         return css`
            background: white;
            border: 1px solid red;
        `
       }
       case "contained": {
         return css`
            background: blue;
            border: 1px solid red;
        `
       }
       default: {
         return css`
            background: blue;
        `
       } 
     }
}}
`;

export const Button = ({ children, className, variant }) =>
  <StyledButton variant={variant} className={className}>
    {children}
  </StyledButton>
```
<br/>
<br/>


### Naming
Styled components should start with prefix `Styled`

Bad
```jsx
import styled from 'styled-components';

const MyButton = styled.button`
`;

export const Button = ({ children}) =>
  <MyButton>
    {children}
  </MyButton>
```

Good
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
`;

export const Button = ({ children, className}) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>
```
<br/>
<br/>


### Props theme
Use getTheme to get props in styled components. <br/>
getTheme uses default theme in case you are using components without ThemeProvider


Bad
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
    color: ${(props) => props.palette.colors.typography.tint1};
`;

export const Button = ({ children, className }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>
```

Good
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
    color: ${(props) => getTheme(props).palette.colors.typography.tint1};
`;

export const Button = ({ children, className }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>
```
<br/>
<br/>


## Theme tokens
Use all the available theme tokens in components like: colors, fonts, spaces, radius, and etc. 

Bad
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
    color: white;
    font-family: Proxima Nova, sans-serif;
    background-color: #6c89f0;
    padding: 2px;
    border-radius: 4px;
`;

export const Button = ({ children, className }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>
```

Good
```jsx
import styled from 'styled-components';

const StyledButton = styled.button`
    color: ${(props) => props.palette.colors.white.main};
    font-family: ${(props) => getTheme(props).typography.F10};
    background-color: ${(props) => props.theme.palette.colors.primary.tint1};
    padding: ${(props) => props.theme.spaces.SP0};
    border-radius: ${(props) => getTheme(props).radius.R20};
`;

export const Button = ({ children, className }) =>
  <StyledButton className={className}>
    {children}
  </StyledButton>
```
<br/>
<br/>



### Export styled components
If you have a simple style component you can export it without creating component.</br/>
In this can it will be just like creating react component that receive children.

Bad
```jsx
import styled from 'styled-components';

const StyledHeader = styled.div`
   height: 30px;
   background: gray;
`;

export const Header = ({ children, className }) =>
  <StyledHeader className={className}>
    {children}
  </StyledHeader>
```

Good
```jsx
import styled from 'styled-components';

export const Header = styled.div`
   height: 30px;
   background: gray;
`;

```
<br/>
<br/>


### Styled components properties
Property that is only used by styled components and shouldn't be in the dom
should be with $.

Bad
```jsx
import styled from 'styled-components';

const StyledHeader = styled.div`
   height: 30px;
   background: ${(props) => props.someProp ? "red" : "blue"};
`;

export const Header = ({ children, className, someProp }) =>
  <StyledHeader someProp={someProp} className={className}>
    {children}
  </StyledHeader>
```

Good
```jsx
import styled from 'styled-components';

const StyledHeader = styled.div`
   height: 30px;
   background: ${(props) => props.$someProp ? "red" : "blue"};
`;

export const Header = ({ children, className, someProp }) =>
  <StyledHeader $someProp={someProp} className={className}>
    {children}
  </StyledHeader>
```
<br/>
<br/>

[Back to top][table-of-contents]


# Code standards

## Destruct your prop

### More than 2 props from an object been used in the same place should be destructed


## Code style
### - Line length should not exceed 80 characters.
### - Best case scenario File length should be under 100 lines.
### - In extreme cases component files should not exceed 250 lines.


## Use explanatory variables
Bad
```js
const onlyNumbersRegex = /^\d+$/
const validateNumber = message => value => !onlyNumbersRegex.test(value) && message

validateNumber('error message')(1)
```

Good
```js
const onlyNumbersRegex = /^\d+$/
const getNumberValidation = message => value => !onlyNumbersRegex.test(value) && message

const isNumber = getNumberValidation('error message')

isNumber(1)
```

## Use intention revealing names
Use intention revealing names instead of number or string

Bad
```js
setTimeout(doSomething, 86400000)
```

Good
```js
const DAY_IN_MILLISECONDS = 1 * 24 * 60 * 60 * 1000;

setTimeout(doSomething, DAY_IN_MILLISECONDS)
```

## One component per file

Bad
```jsx
import styled from 'styled-components';

export const Tab = ({ children, className }) =>
  <StyledTab className={className}>
    {children}
  </StyledTab>

export const TabsHeader = ({ children, className }) =>
  <StyledTabsHeader className={className}>
    {children}
  </StyledTabsHeader>
```

Good

File: Tab.tsx
```jsx
import styled from 'styled-components';

export const Tab = ({ children, className }) =>
  <StyledTab className={className}>
    {children}
  </StyledTab>
```


File: TabsHeader.tsx
```jsx
import styled from 'styled-components';

export const TabsHeader = ({ children, className }) =>
  <StyledTabsHeader className={className}>
    {children}
  </StyledTabsHeader>
```


[Back to top][table-of-contents]


## CSS Design Patterns

### The parent constrains the child

Leaf components shouldn't constrain width or height (unless it makes
sense).


Bad
```jsx
import styled from 'styled-components';

const StyledInput = styled.input`
   box-sizing: border-box;
   padding: 10px;
   width: 140px;
`;

export const Input = ({ children, className }) =>
  <StyledInput className={className}>
    {children}
  </StyledInput>
```

Good
```jsx
import styled from 'styled-components';

const StyledInput = styled.input`
   box-sizing: border-box;
   padding: 10px;
   width: 100%;
`;

export const Input = ({ children, className }) =>
  <StyledInput className={className}>
    {children}
  </StyledInput>
```
<br/>
<br/>

[Back to top][table-of-contents]


### Components never leak margin

All components are self-contained and their final size should 
never suffer margin leakage! 
This allows the components to be much more reusable!



<table>
<thead>
  <th><strong>BAD</strong></th>
  <th><strong>GOOD</strong></th>
</thead>
<tbody>
<tr>
<td>

```
|--|-content size-|--| margin
 ____________________
|   ______________   | | margin
|  |              |  |
|  |              |  |
|  |              |  |
|  |______________|  |
|____________________| | margin

|---container size---|
```

</td>

<td>

```

   |-content size-|
    ______________
   |              |
   |              |
   |              |
   |______________|



```

</td>
</tr>
</tbody>
</table>


Bad
```jsx
import styled from 'styled-components';

const StyledLoader = styled.div`
   box-sizing: border-box;
   width: 50px;
   height: 50px;
   margin-top: 100px;
`;

export const Loader = ({ children, className }) =>
    <StyledLoader className={className}/>
```

Good
```jsx
import styled from 'styled-components';

const StyledLoader = styled.div`
   box-sizing: border-box;
   width: 50px;
   height: 50px;
`;

export const Loader = ({ children, className }) =>
  <StyledLoader className={className}/>


//Usage
const MyLoader = styled(Loader)`
   margin-top: 100px;
`;

return <MyLoader/>

```

[Back to top][table-of-contents]

<br/>


### The parent spaces the children

When building lists or grids:

* Build list/grid items as separate components
* Use the the list/grid container to space children
* To space them horizontally, use `margin-left`
* To space them vertically, use `margin-top`
* Select the `first-child` to reset margins

Example
```jsx
import styled from 'styled-components';

const StyledImage = styled.img`
   margin-left: 10px;
   
   :first-chld { 
      margin-left: unset;
   }
`;

export const Reviews = ({ items, className }) =>
  <div className={className}>
    {items.map(item =>
      <StyledImage src={item.image} alt={item.title} />
    )}
  </div>

```

[Back to top][table-of-contents]




# Components Design Patterns


## Use Controlled components
Controlled components takes its current value through props <br/>
and makes changes through callbacks like onClick, onChange, etc...
When possible use Controlled components instead of Uncontrolled ones.
Since most of the time we have initial state and everytime the input changes 
there are actions triggered.


Uncontrolled
```jsx
import { useState } from 'react';
import styled from 'styled-components';

const StyledInput = styled.div`
   height: 30px;
`;

export const Search = forwardRef(({ children, className }), ref) =>
  <StyledInput className={className} ref={ref}/>


//Usage
const inputRef = useRef(null);
const value = inputRef.current.value
render <Search ref={inputRef}/>
```

Controlled
```jsx
import { useState } from 'react';
import styled from 'styled-components';

const StyledInput = styled.div`
   height: 30px;
`;

export const Search = ({ value, onChange, className }) => {
  return <StyledInput value={value} className={className} onChange={(e) => onChange(e.target.value)} />
}
```

[Back to top][table-of-contents]



## Do not use redundant state
Do not create state in the component in case value and onchange is passed.


Bad
```jsx
import { useState } from 'react';
import styled from 'styled-components';

const StyledInput = styled.div`
   height: 30px;
`;

export const Search = ({ value, onChange, className }) => {
  const [text, setText] = useState<string>(value);
  
  const handleChange = (e) => {
    setText(e.target.value);
    onChange(e.target.value)
  }
  
  return <StyledInput value={value} className={className} onChange={onChange}/>
}
  
```

Good
```jsx
import { useState } from 'react';
import styled from 'styled-components';

const StyledInput = styled.div`
   height: 30px;
`;

export const Search = ({ value, onChange, className }) => {
  return <StyledInput value={value} className={className} onChange={(e) => onChange(e.target.value)}/>
}
```

[Back to top][table-of-contents]



## API design

### Children instead of properties

Bad
```jsx
<Header title="Hi" />
```

Good
```jsx
<StyledHeader>
  <Title>Hi</Title>
</StyledHeader>
```

### Composability
Components should be built in a composable way. <br/>
Larger, more complex components should be built using smaller, simple components. <br/>
And those larger components should export those sub-components. <br/>
This will allow users to drop a level below the higher abstraction <br/>
and compose the sub-components in a way to meet their use case. <br/>
Example: Mui Design API 

Bad
```jsx
<Menu
  label="Animals"
  options={options} 
  noSearchResults={<div>Custom No Results</div>}
  placeholder={"Select"}
  onChange={onChange}
  optionsHeight={400}
/>
```

1. But what if I need an icon in the Menu’s title  
2. What if I want different style for menu items
3. And different style search
4. Is the search is on the server side
5. Menu items with checkboxes

Good <br/>
We create Menu and MenuItem and we can compose any menu from it.<br/>
We can style MenuItem, put icons before and after and to add checkmark.

```jsx
export const Menu = () => ...
export const MenuItem = () => ...

<Menu>
  <MyMenuTitle>
  {items.map((item) => {
    const isSelected = selected === item.value;
    return (
      <MenuItem key={item.value} onClick={(e) => handleClick(e, item.value)} selected={isSelected} checkMark>
        <MenuItemStartIcon selected={isSelected}>
          <item.startIcon />
        </MenuItemStartIcon>
        <MenuItemText selected={isSelected}>{item.label}</MenuItemText>
      </MenuItem>
    );
  })}
</Menu>
```

[Back to top][table-of-contents]



#  Typography

### Always use Typography component when adding a text

Bad
```jsx
import styled from 'styled-components';

const StyledText = styled.p`
    font-family: ${(props) => getTheme(props).typography.F10};
    font-style: normal;
    font-weight: 400;
    font-size: 14px;
    line-height: 20px;
`;

export const Title = ({ children, className }) =>
  <StyledText className={className}>
    {children}
  </StyledText>
```

Good

```jsx
import styled from 'styled-components';
import { Typography } from './Typography';

export const Title = ({ children, className }) =>
  <Typography variant="T30" className={className}>
    {children}
  </Typography>


//or if you need div element 
export const Title = ({ children, className }) =>
<Typography as="div" variant="T30" className={className}>
  {children}
</Typography>
```

<br/>




[table-of-contents]: #table-of-contents
