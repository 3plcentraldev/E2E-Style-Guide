# 3PLCentral E2E Style Guide

* A mostly reasonable approach to E2E 

## Table of Contents
  1. [Running](#running)
  2. [Tests](#tests)
  3. [Get](#selection)
  4. [Visual Assertions](#vassertions)
  5. [Actions (clicking/selecting/inputing)](#actions)
  6. [Calling API](#api)
  7. [Grids](#grids)

## Running

  <a name="types--primitives"></a><a name="1.1"></a>
  - [1.1](#types--primitives) **Tests**: Running the E2E tests
	
	- The easiest way to write and update tests is to use cypress command that will run and show you the UI as you do
	- node or some other mechanism will be used to run the CI/CD tests or to run them in their entirety
	

    ```javascript
    // good
    npx cypress open --env STAGE=luigi
    
    // bad
    node app.js
    ```

## Tests

  <a name="types--primitives"></a><a name="2.1"></a>
  - [2.1](#types--primitives) **Tests**: Organizing Tests
	
	- There are 2 things you can use for organizing tests, describe and it.  'it' is the actual test, 'describe' is a way to organize it.  For example Perhaps you had a module that was testing the Find Orders Page, you wanted to have 1 section that was just testing the filtering section you might write it like this below
	

    ```javascript
    describe('Cypress Tests for Orders', function(){
    
  	       describe('Filter Tests', function(){
		    it('should have a date filter field', function(){
	    
		    })
		});
		
		describe('Grid Tests', function(){
	
		});
    })
    ```

**[⬆ back to top](#table-of-contents)**

## Get

  <a name="types--primitives"></a><a name="3.1"></a>
  - [3.1](#types--primitives) **Selections**: When you select something from the DOM
	
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

  <a name="references--prefer-const"></a><a name="4.1"></a>
  - [4.1](#references--prefer-const) Visual Assertions https://docs.cypress.io/api/commands/should.html#Syntax

    - Visual assertions help you determine if something is visually what you expect and helps you navigate your flow.  For example you may need to wait til something is visible before clicking it
	- Visual assertions require a valid selection, shoulds will wait until the condition is true before moving on
	- Sometimes a input field might not be in view if you are trying to see it so you might see an error like this 
	- the element is not visible because it has CSS property: 'position: fixed' and its being covered by another element
    ```javascript
    // bad
    cy.wait(5000)
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible')

    // good
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible')
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').scrollIntoView().should('be.visible')
    ```

    ```javascript
    // common usages    
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible')
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.disabled')
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.enabled')
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('have.css', 'font-family')

    ```


**[⬆ back to top](#table-of-contents)**

## Actions

  <a name="references--prefer-const"></a><a name="5.1"></a>
  - [5.1](#references--prefer-const) Clicking https://docs.cypress.io/api/commands/click.html#Syntax

    - Clicking is pretty simple, however you should make sure your expectations or correct before trying
	
    ```javascript
    // bad
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').click()

    // good
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible').click()
    ```

    - Normally this should be enough, however there may be times where you need to force the UI to click something, whether it is showing or not.  Sometimes you may run into a situation, where it is showing as visible but for whatever reason cypress has a problem clicking it, in that case you should use force: true

    ```javascript
    cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible').click({ force: true })
    ```

  <a name="references--prefer-const"></a><a name="5.2"></a>
  - [5.2](#references--prefer-const) Selecting https://docs.cypress.io/api/commands/select.html#Syntax

    - Selecting can select the name or value, name being what is displayed to the browser and value is the underlining data
	
    ```javascript
	<select>
	  <option value="456">apples</option>
	  <option value="457">oranges</option>
	  <option value="458">bananas</option>
	</select>
	
	cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible').select('apples');
	cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible').select('456');
	
    ```

    - Some kendo dropdowns may require you to do something different
	- This would be used when using a kendo virtual dropdown that has a search bar as well in it
    ```javascript
	cy.get('[data-wms-selector="DropdownSelector"]').should('be.visible').then((_elem) => {
	   _elem.parents('span').click();
	   cy.get('input[aria-owns="SkuDropDownListReceiptAddLineItem_listbox"]').type(itemName, {
              force: true
           });
	
	});

	
    ```

    
      <a name="references--prefer-const"></a><a name="5.3"></a>
  - [5.3](#references--prefer-const) Selecting https://docs.cypress.io/api/commands/type.html#Syntax

    - You can use type to actually fill out an element, that being said it will ADD to it, so the preference is you always clear the value of the field first then type in the totality, this basically means you replace what is there with what you want
	
    ```javascript
	// bad
	cy.get('[data-wms-selector="Warehouse_MainMenu"]').type('Iroquais Drive');
	
	// good
	cy.get('[data-wms-selector="Warehouse_MainMenu"]').should('be.visible').clear().type('Iroquais Drive');
	
    ```



**[⬆ back to top](#table-of-contents)**

## API

  <a name="references--prefer-const"></a><a name="6.1"></a>
  - [6.1](#references--prefer-const) API https://docs.cypress.io/api/commands/request.html#Syntax

    - It is often the case that it is best to call the API instead of using the UI to set up data for your tests.  In other words if you need to have an item setup to test something in receipts, it would be better to use the API under the hood to setup your data as needed, then to try to do it through the UI, since you are not trying to test Items you are trying to test receipts
    - You should generally call the url of the stage you are on, in other words, wms.getBaseUrl would return localhost if you are local etc, and something else if it is ran on luigi, but the second parameter is just a url
	
    ```javascript
    let myobj = {"orderId": 5};
    cy.request('POST', wms.getBaseUrl() + '/WebUI/Settings/ImportOrderPreferences/GetPreferences', myobj)
    .then((response) => {
         // You would write actions in here
         // You might check to see if it returned 200 by something like this
         expect(response.status).to.eq(200);
    });
    ```

   
**[⬆ back to top](#table-of-contents)**

## Grids

  <a name="references--prefer-const"></a><a name="7.1"></a>
  - [7.1](#references--prefer-const) Grids

    - Use grids can be difficult, here is a easy command to select the first element in a grid, it is often best to filter to just one element in a grid and select it

	
    ```javascript
    // right click for context menu
     cy.get('[data-wms-selector="findCustomerGrid"]').scrollIntoView().find('.k-grid-content tbody tr').eq(0).should('be.visible').trigger('contextmenu');
     
     
    // click row
    cy.get('[data-wms-selector="findCustomerGrid"]').scrollIntoView().find('.k-grid-content tbody tr').eq(0).should('be.visible').click();
    ```

   
**[⬆ back to top](#table-of-contents)**
