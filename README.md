# 3PLCentral E2E Style Guide

* A mostly reasonable approach to E2E 

## Table of Contents

  1. [Selections](#selection)
  2. [Visual Assertions](#vassertions)


## Selections

  <a name="types--primitives"></a><a name="1.1"></a>
  - [1.1](#types--primitives) **Selections**: When you select something from the DOM
	
	- You should always select something using the data-wms-selector attribute with the exception of custom elements in other libraries like Kendo.  Our components require you to specify data-wms-selector so it should be available everywhere.
	

    ```javascript
    // bad
	cy.get('#Warehouse_MainMenu');
	
	// bad
	cy.get('.Warehouse');
	
	// good
	cy.get('[data-wms-selector="Warehouse_MainMenu"]');
    ```

**[⬆ back to top](#table-of-contents)**

## Visual Assertions

  <a name="references--prefer-const"></a><a name="2.1"></a>
  - [2.1](#references--prefer-const) Visual Assertions

    - Visual assertions help you determine if something is visually what you expect and helps you navigate your flow.  For example you may need to wait til something is visible before clicking it
	- Visual assertions require a valid selection, shoulds will wait until the condition is true before moving on
	
    ```javascript
    // bad
    cy.wait(5000)
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible')

    // good
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible')
    ```



**[⬆ back to top](#table-of-contents)**
