# An overview of accessible web applications and widgets

The web is changing. Static, page-based sites are increasingly being replaced by dynamic, desktop-style web applications that make heavy use of JavaScript and AJAX. Designers are creating amazing new widgets and controls entirely with a combination of JavaScript, HTML, and CSS. This shift has the potential to dramatically improve the responsiveness and usability of the web, but many users are at risk of being excluded due to accessibility gaps. JavaScript has traditionally had a reputation for being inaccessible to users of assistive technologies such as screen readers, but there are now ways to create dynamic web user interfaces that are accessible to a wide variety of users.

The problem
Most JavaScript toolkits offer a library of client-side widgets that mimic the behavior of familiar desktop interfaces. Sliders, menu bars, file list views, and more can be built with a combination of JavaScript, CSS, and HTML. Since the HTML 4 specification doesn't provide built-in tags that semantically describe these kinds of widgets, developers typically resort to using generic elements such as <div> and <span>. While this results in a widget that looks like its desktop counterpart, there usually isn't enough semantic information in the markup to be usable by an assistive technology. Dynamic content on a web page can be particularly problematic for users who, for whatever reason, are unable to view the screen. Stock tickers, live twitter feed updates, progress indicators, and similar content modify the DOM in ways that an assistive technology (AT) may not be aware of. That's where ARIA comes in.

Example 1: Markup for a tabs widget built without ARIA labeling. There's no information in the markup to describe the widget's form and function.

<!-- This is a tabs widget. How would you know, looking only at the markup? -->
<ol>
  <li id="ch1Tab">
    <a href="#ch1Panel">Chapter 1</a>
  </li>
  <li id="ch2Tab">
    <a href="#ch2Panel">Chapter 2</a>
  </li>
  <li id="quizTab">
    <a href="#quizPanel">Quiz</a>
  </li>
</ol>

<div>
  <div id="ch1Panel">Chapter 1 content goes here</div>
  <div id="ch2Panel">Chapter 2 content goes here</div>
  <div id="quizPanel">Quiz content goes here</div>
</div>
Example 2: How the tabs widget might be styled visually. Users might recognize it visually, but there are no machine-readable semantics for an assistive technology.
Screenshot of the tabs widget

ARIA
WAI-ARIA, the Accessible Rich Internet Applications specification from the W3C's Web Accessibility Initiative, provides a way to add the missing semantics needed by assistive technologies such as screen readers. ARIA enables developers to describe their widgets in more detail by adding special attributes to the markup. Designed to fill the gap between standard HTML tags and the desktop-style controls found in dynamic web applications, ARIA provides roles and states that describe the behavior of most familiar UI widgets.

The ARIA specification is split up into three different types of attributes: roles, states, and properties. Roles describe widgets that aren't otherwise available in HTML 4, such as sliders, menu bars, tabs, and dialogs. Properties describe characteristics of these widgets, such as if they are draggable, have a required element, or have a popup associated with them. States describe the current interaction state of an element, informing the assistive technology if it is busy, disabled, selected, or hidden.

ARIA attributes are designed to be interpreted automatically by the browser and translated to the operating system's native accessibility APIs. When ARIA is present, assistive technologies are able to recognize and interact with custom JavaScript controls in the same way that they do with desktop equivalents. This has the potential for providing a much more consistent user experience than was possible in the previous generation of web applications, since assistive technology users can apply all of their knowledge of how desktop applications work when they are using web-based applications.

Example 3: Markup for the tabs widget with ARIA attributes added.

<!-- Now *these* are Tabs! -->
<!-- We've added role attributes to describe the tab list and each tab. -->
<ol role="tablist">
  <li id="ch1Tab" role="tab">
    <a href="#ch1Panel">Chapter 1</a>
  </li>
  <li id="ch2Tab" role="tab">
    <a href="#ch2Panel">Chapter 2</a>
  </li>
  <li id="quizTab" role="tab">
    <a href="#quizPanel">Quiz</a>
  </li>
</ol>

<div>
  <!-- Notice the role and aria-labelledby attributes we've added to describe these panels. -->
  <div id="ch1Panel" role="tabpanel" aria-labelledby="ch1Tab">Chapter 1 content goes here</div>
  <div id="ch2Panel" role="tabpanel" aria-labelledby="ch2Tab">Chapter 2 content goes here</div>
  <div id="quizPanel" role="tabpanel" aria-labelledby="quizTab">Quiz content goes here</div>
</div>
ARIA is supported in the latest versions of all major browsers, including Firefox, Safari, Opera, Chrome, and Internet Explorer. Many assistive technologies, such as the open source NVDA and Orca screen readers, also support ARIA. Increasingly, JavaScript widget libraries such as jQuery UI, YUI, Google Closure, and Dojo Dijit include ARIA markup as well.

Presentational changes
Dynamic presentational changes include using CSS to change the appearance of content (such as a red border around invalid data, or changing the background color of a checked checkbox), as well as showing or hiding content.

State changes
ARIA provides attributes for declaring the current state of a UI widget. Examples include (but are certainly not limited to):

aria-checked: indicates the state of a checkbox or radio button
aria-disabled: indicates that an element is visible, but not editable or otherwise operable
aria-grabbed: indicates the 'grabbed' state of an object in a drag-and-drop operation
(For a full list of ARIA states, consult the ARIA list of states and properties.)

Developers should use ARIA states to indicate the state of UI widget elements and use CSS attribute selectors to alter the visual appearance based on the state changes (rather than using script to change a class name on the element).

Note: This Example(http://www.oaa-accessibility.org/example/25/) is no more available. For State changes,  please see the W3C ARIA Authoring Practices guide examples (www.w3.org/TR/wai-aria-practices-1.1/examples/checkbox/checkbox-1/checkbox-1.html)

The Open Ajax Alliance website provides an example of CSS attribute selectors based on ARIA states. The example shows a WYSIWYG editor interface with a dynamic menu system. Items currently selected in a menu, such as the font face, are visually distinguished from other items. The relevant parts of the example are explained below.

In this example, the HTML for a menu has the form shown in Example 1a. Note how, on lines 7 and 13, the aria-checked property is used to declare the selection state of the menu items.

Example 1a. HTML for a selectable menu (adapted from http://www.oaa-accessibility.org/example/25/).

Visibility changes
When content visibility is changed (i.e., an element is hidden or shown), developers should change the aria-hidden property value. The techniques described above should be used to declare CSS to visually hide an element using display:none.

The Open Ajax Alliance website provides an example of a tooltip that uses aria-hidden to control the visibility of the tooltip. The example shows a simple web form with tooltips containing instructions associated with the entry fields. The relevant parts of the example are explained below.

In this example, the HTML for the tooltip has the form shown in Example 2a. Line 9 sets the aria-hidden state to true.

Example 2a. HTML for a tooltip (adapted from http://www.oaa-accessibility.org/example/39/).

<div class="text">
    <label id="tp1-label" for="first">First Name:</label>
    <input type="text" id="first" name="first" size="20"
           aria-labelledby="tp1-label"
           aria-describedby="tp1"
           aria-required="false" />
    <div id="tp1" class="tooltip"
         role="tooltip"
         aria-hidden="true">Your first name is optional</div>
</div>
The CSS for this markup is shown in Example 2b. Note that there is no custom classname used, only the status of the aria-hidden attribute on line 1.

Example 2b. Attribute-based selector for indicating state (from http://www.oaa-accessibility.org/example/39/).

div.tooltip[aria-hidden="true"] {
  display: none;
}
The JavaScript to update the aria-hidden property has the form shown in Example 2c. Note that the script only updates the aria-hidden attribute (line 2); it does not need to also add or remove a custom classname.

Example 2c. JavaScript to update the aria-checked attribute (based on http://www.oaa-accessibility.org/example/39/).

var showTip = function(el) {
  el.setAttribute('aria-hidden', 'false');
}
Role changes
Under construction
ARIA allows developers to declare a semantic role for an element that otherwise offers incorrect or no semantics. For example, when an unordered list is used to create a menu, the <ul> should be given a role of menubar and each <li> should be given a role of menuitem.

The role of an element should not change. Instead, remove the original element and replace it with an element with the new role.

For example, consider an "inline edit" widget: a component that allows users to edit a piece of text in place, without switching contexts. This component has a "view" mode, in which the text is not editable, but is activatable, and an "edit" mode, in which the text can be edited. A developer might be tempted to implement the "view" mode using a read-only text  <input> element and setting its ARIA role to button, then switching to "edit" mode by making the element writable and removing the role attribute in "edit" mode (since <input> elements have their own role semantics).

Do not do this. Instead, implement the "view" mode using a different element altogether, such as a <div> or <span> with a role of button, and the « edit » mode using a text  <input> element.

Asynchronous content changes
Under construction. See also Live Regions
Keyboard navigation
Often times developers overlook support for the keyboard when they create custom widgets. To be accessible to a variety of users, all features of a web application or widget should also be controllable with the keyboard, without requiring a mouse. In practice, this usually involves following the conventions supported by similar widgets on the desktop, taking full advantage of the Tab, Enter, Spacebar, and arrow keys.

Traditionally, keyboard navigation on the web has been limited to the Tab key. A user presses Tab to focus each link, button, or form on the page in a linear order, using Shift-Tab to navigate backwards. It's a one-dimensional form of navigation—forward and back, one element at a time. On fairly dense pages, a keyboard-only user often has to press the Tab key dozens of times before accessing the needed section. Implementing desktop-style keyboard conventions on the web has the potential to significantly speed up navigation for many users.

Here's a summary of how keyboard navigation should work in an ARIA-enabled web application:

The Tab key should provide focus to the widget as a whole. For example, tabbing to a menu bar should put focus on the menu's first elem.
The arrow keys should allow for selection or navigation within the widget. For example, using the left and right arrow keys should move focus to the previous and next menu items.
When the widget is not inside a form, both the Enter and Spacebar keys should select or activate the control.
Within a form, the Spacebar key should select or activate the control, while the Enter key should submit the form's default action.
If in doubt, mimic the standard desktop behavior of the control you are creating.
So, for the Tabs widget example above, the user should be able to navigate into and out of the widget's container (the <ol> in our markup) using the Tab and Shift-Tab keys. Once keyboard focus is inside the container, the arrow keys should allow the user to navigate between each tab (the <li> elements). From here, conventions vary from platform to platform. On Windows, the next tab should automatically be activated when the user presses the arrow keys. On Mac OS X, the user can press either Enter or the Spacebar to activate the next tab. An in-depth tutorial for creating Keyboard-navigable JavaScript widgets describes how to implement this behavior with JavaScript.

For more detail about desktop-style keyboard navigation conventions, a comprehensive DHTML style guide is available. It provides an overview of how keyboard navigation should work for each type of widget supported by ARIA. The W3C also offers a helpful ARIA Best Practices document that includes keyboard navigation and shortcut conventions for a variety of widgets. 


### overlap of ARIA with HTML5

New HTML 5 elements and corresponding ARIA roles
HTML 5	ARIA Role
```
<header>	role="banner"
<nav>	role="navigation"
<main>	role="main"
<footer>	role="contentinfo"
<aside>	role="complementary"
<section>	role="region" *
<article>	role="article" *
none	role="search"
<form>	role="form"
```

### Resources:
[An overview of accessible web applications and widgets](https://developer.mozilla.org/en-US/docs/Web/Accessibility/An_overview_of_accessible_web_applications_and_widgets)


[Keyboard-navigable JavaScript widgets](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Keyboard-navigable_JavaScript_widgets)

[Accessibility Examples-treeView](http://www.oaa-accessibility.org/examplep/treeview1/)

[Accessibility Examples-RadioGroup](http://www.oaa-accessibility.org/example/28/)

[WCAG 2.0 checklists](https://wuhcag.com/wcag-checklist/)

[WAI-ARIA-PRACTICES](https://elemefe.github.io/WAI-ARIA-Practices/#/aria)

[ARIA in HTML](https://www.w3.org/TR/html-aria/#allowed-aria-roles-states-and-properties-1)

[Accessibility in React](https://reactjs.org/docs/accessibility.html)

[Accessible Rich Internet Applications (WAI-ARIA) 1.1](https://www.w3.org/TR/wai-aria/states_and_properties)

[Keyboard Accessibility](https://webaim.org/techniques/keyboard/)

[voiceover key command](https://webaim.org/articles/voiceover/)

[voice command](http://lab.dotjay.com/notes/voiceover-commands/)