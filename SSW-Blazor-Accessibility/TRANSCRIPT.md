# Accessify Your Blazor Apps Hot Accessibility Techniques For Blazor Apps

Hi everyone thanks for joining this evening under what are very challenging circumstances. This was to be a live session in Sydney and Canberra but now its online. However, I've got more flexibility to help answer some questions as we go, I'll be taking questions throughout the presentation via Twitter. If you have any questions tag me in them [@MrRossMullen](https://twitter.com/MrRossMullen) with the hashtag #SSWBlazorA11y. 
For those in the know and those not, a11y is a shorthand way for discussing web accessibility on Twitter without using up all of the available characters. There are 11 characters between the A and Y hence Aye-Eleven-Why or a11y.

The agenda this evening is
* Brief recap on accessibility
* Frankenstein's monster, the `DIV` button
* 3rd party control suites
* Aria live regions
* Managing accessibility in large teams
* And lastly using Javascript interop to manage screen focus

A little about me, I'm a digital accessibility specialist in the public service and I'm also director of CANAXESS a web and digital accessibility company. I've been working in web accessibility for in excess of 7 years in a myriad of roles including accessibility auditor and evaluator, and accessibility developer which is what I mainly do now. Before that I had numerous roles as a C# developer gradually moving from the backend and then into the frontend. I'm also a web accessibility author for Pluralsight and create accessibility courses on Udemy and YouTube.

## Brief Recap On Accessibility
Accessibility is making an application usable by everyone with or without impairments. Many of you have heard about WCAG. It's best practice accessibility techniques which set out how to make web content accessible for the largest number of people. It's a set of technical criteria to build against, grouped under guidelines and principles which categorise the technical criteria into areas of concern.

WCAG stands for web content accessibility guidelines and it's an internationally accepted best practice way of making web content accessible. It's been endorsed by governments and large organisations worldwide as the accepted technical standard to apply. Its technology agnostic, but ultimately presents solutions in HTML, CSS and Javascript.

WCAG has three levels of conformance A, AA and AAA when implementing the technical detail. These are degrees of difficulty of implementing a technique and support for the end user. Single A techniques are pretty straight forward to apply technically, and give the user modest accessibility support. Whereas triple A techniques can support more users with more complex and diverse impairments but it's also more technically challenging to implement.

For example, providing ALT text for images means people with a vision impairment can understand it and its quite trivial to implement.
Whereas a triple A technique is providing a sitemap. It can help users navigate a site in a nonlinear way but providing all possible permutations of a complex web app is much more challenging. Double A criteria is identified as that sweet spot, providing good accessibility support and not a massive technical obstacle to implement.

Web accessibility is a big deal. In the US Beyoncé Knowles' company was taken to court over an inaccessible website as a blind user wasn’t able to use it as was the US pizza chain Dominoes. Whilst there's been far less court cases in Australia there have been a few standouts. The supermarket chain Coles had an online ordering component which blind people couldn’t use, after repeated attempts to rectify, the case eventually ended up in Court and Coles settled with an agreement to fix their website.

## Frankenstein's Monster, The `DIV` Button
A challenge when building complex interfaces is the standard array of HTML elements don’t always provide the level of interactivity needed. A decision is often made to build a new control as it'll provide more flexibility and it can do more and work how we want it to work. But there is a problem with this approach. 

The standard HTML elements have built in semantics, the controls play well with browsers and assistive technologies (so things like screen readers) out of the box, there's no setup needed, no configuration, native HTML components just work. When a `BUTTON` element is used, the screen reader understands it’s a button, the browser allows it to be focusable in the natural page sequence and its state when it is pressed is announced through to the screen reader through the accessibility API which exists at the operating system level. The operating system exposes the webpage objects and events to assistive technology through this API. All this hidden accessibility support is provided simply by using a button element or any other native HTML component.

But someone may come along and say buttons are boring, that they can't style them adequately and work begins making a new control with `DIV` and `SPAN` elements. Using these generic elements means you can begin building richer UI's with greater flexibility, but this comes at a significant disadvantage for assistive technology users. All of those inbuilt semantics which existed when using a native controls, are now lost as we've decided to go it alone. 

The `DIV` element probably has a click event triggered when using the mouse, but when using a keyboard its unreachable because `DIV` elements are generic and don’t appear in the keyboard ordering sequence. Some users navigate predominantly via the keyboard and they now can't tab and action the control.

So, first of all we have to add onto the `DIV` the `tabindex` attribute with a value of `0` to ensure its focusable in the keyboard tab sequence. `tabindex="0"` means the element appears in the regular keyboard sequence and that sequence is in the order the components are displayed onscreen.

Adding a `tabindex` attribute and a value of `0` to the `DIV` has now made it focusable from the keyboard, users can tab to the control. Whilst its now actionable assuming keyboard and mouse event handlers have also been added when a screen reader encounters it, it won't describe what the element is when in focus. Native elements announce the type of control audibly through the screen reader when in focus because of their built-in semantics, it may announce the type of control such as button or link. 

However, `DIV` and `SPAN` elements don’t. Our hybrid control is a `DIV` and `DIV` elements are generic and don’t do anything so whilst its clickable a vision impaired user relying on a screen reader to communicate the controls audibly won't know it can be clicked. 

To overcome this, we add the attribute `role` and the value of `button`. Role attributes are special attributes called aria (accessible rich internet applications) which can make elements assume the behaviour of other elements. By assigning the `role` of `button` to a `DIV` we can trick the screen reader and other assistive technology into announcing that this `DIV` is actually a button and buttons are clickable. So, we've now got two wins, we've added `tabindex` to make the faux button navigable from the keyboard and we've used `role=button` to make the screen reader announce it’s a button. 

But this `DIV` button may have an icon instead of text denoting the button does something, which is great for everyone who can see it but if you can't well, we need to cater for that. We have to provide an accessible name, which is a label that provides a text description for the button. If this button received focus without a text description nothing would be communicated to the user other than the control type. To overcome this, we apply the aria-label attribute to provide a programmatic text description. This description isn't visible, but it means when in focus navigating via a screen reader this text description will be communicated to the user. 

It's as this point the faux button is now behaving like a regular button. It has a `tabindex`, a `role` of `button` and an accessible name, and it's got click and keypress event handlers. All this work to make this Frankenstein's monster button be replaced with, using a `BUTTON` element.

A `BUTTON` element has a default behaviour of being focusable from the keyboard, it has an implied role announced to assistive technology and often (if created correctly) has a text label. So, we can go ahead and make custom controls but the effort to make them accessible and practically usable is significantly greater and much more effort. 

By sticking with native HTML controls and components our apps are already more accessible than if building a custom control. That isn't to say custom controls should be avoided, but its understanding that to make those controls accessible is much more challenging.

## The difficulty with 3rd party control suites
But the reality is there are times when we need to build complexity, that the controls provided by native HTML are too basic for what we need. We might need filter and sort functionality on data tables, autosuggest controls and other functionality which can't be provided by native HTML. After understanding all the challenges of building your own `DIV` button and the steps you have to go through, you might then think use a third-party custom control suite as to build it ourselves is really really challenging. Vendors have done the hard work for us by building a suite of complex custom controls and due diligence has been applied by making sure the control suite has been built to accessible standards, the vendors even provide an accessibility conformance statement.

Be very cautious!

This is one of the most problematic areas of using suites of custom controls to do a lot of the heavy lifting in web apps. Telerik, Synchfusion, Infragistics and many other vendors produce feature rich controls, which seamlessly integrate into your existing code and make it easy to produce complex interactions. 

However, the accessibility statements the vendors provide are often so narrowly defined and superficial that the accessibility support provided is extremely limited and, in some instances, non-existent. The accessibility statement may state a control is keyboard accessible or that is has aria support for screen readers. Unfortunately, the accessibility statement provided applies the accessibility guidelines in isolation. So, whilst a control may have keyboard support and can be reachable from a keyboard, it may not have an accessible name. The control may trigger a partial refresh of the page but is that refresh communicated to a screen reader through an aria live region. 

Looking at the Telerik UI for Blazor grid "Add Product" is the only button. All filter controls are `DIV` elements. Each filter control has a label of filter, but not what column is filtered. As they're all labelled the same a screen reader users will only hear multiple "filter" and thats if they could reach the control. Each cell has buttons which allow you to Edit or Delete the row, but these have a `tabindex="-1"` attribute which renders the buttons unfocusable from the keyboard. If the table refreshes this change isn't communicated audibly through the screen reader using a live region. And this has just been a very quick superficial check. The more you look, the more of a rabbit hole you go down in finding really challenging accessibility problems. 

And it's not just Telerik, its all of the well-known custom control vendors. Accessibility is viewed as technical checklist, which is assumed that by putting in more accessibility linting the control is made accessible and supports more users with diverse disabilities. Unfortunately, it doesn’t work like that. 

To build in accessibility means having to think about not only making sure the control and its subcomponents are accessible but making its effect and result is accessible as well. And backing this up with testing with users with disabilities. And ensuring all that works in a combination of browsers and assistive technologies. It's a lot of effort to get right and that’s why unfortunately many haven’t got it right.

It's promising that vendors are understanding accessibility is a growing requirement everywhere. But before you adopt a custom control suite, it needs to be comprehensively tested for accessibility, so you at least know that there is a suitable level of support there. If you don’t have time to independently test the control yourselves, ask if the vendor has tested the controls with users with a range of impairments and request to see the outcome.

## Alerting the user audibly
The way we develop apps now means we seldom trigger a hard refresh of the page, only the parts of the page which have changed are refreshed. Because this change is visual a screen reader can't identify which parts of the page have updated as it relies upon the hard refresh for new content to enter its buffer. Something has changed, or something has refreshed and we need to look at other techniques to communicate the page state. One approach is to provide audible hints through an accessibility technique called a live region which can be used to indicate the status of the change audibly to the screen reader.

A live region is really just adding an `aria-live` attribute which screen readers can interpret to any HTML container which has content that updates. It could be a div or span element, really anything. The way the live region works is when content is added to it, the new content is communicated through to the screen reader which announces it so it’s a very convenient way to alert the user that something has changed. You can use one of several values which prioritises this announcement. If a value of `assertive` was used this would cause the screen reader to announce the new content straight away, if a screen reader was in the middle of reading onscreen content and new content was inserted into the live region, the screen reader will stop its announcing of other content and immediately announce the new content in the container. 

We can dial down the level of urgency and instead use the value `polite`, which will pause until all other onscreen announcements have first finished before announcing the newly updated content. 

We can also control the types of updates for the live region. Using the `aria-relevant` attribute we can announce only new content added, content removed, text updated or everything. 

By default, without doing anything the `aria-live` attribute behaviour is watching for additions and text. There may be times where we want to only announce new content, the container may have existing content like a status message which holds all the old messages but we don’t want all the previous content announced when new content is added, and we can add the attribute `aria-atomic`. This takes a boolean value which specifies all of the contents in the container is announced, or only the new content.

An aria live region provides tremendous flexibility with how to communicate onscreen updates to a screen reader. And it may be confusing with the numerous variations of `aria-atomic` values, and `aria-relevant` values and the levels of assertiveness. A technique in the updated Web content accessibility guidelines version 2.1 provides pre-defined roles which have all the behaviour in place and we just need to specify the role according to what type of update it is. The roles all have different behaviour types for specific purposes. The roles are:

* `Log` is for describing a log of events, or messages. It has an implied assertiveness level of polite, and an implied atomic value of false so only new updates are announced.
* `Status` is for communicating status updates, a document has been saved or a screen has loaded. It has an implied assertiveness level of polite, and an implied atomic value of true so when new content is inserted into the container everything is announced.
* `Alert` is for communicating errors or warnings. It has an implied assertiveness level of assertive, and an implied atomic value of true so everything is announced.

There are also the roles `Progress Bar`, `Marquee` and `Time` and there is still debate as to how screen readers and assist technology interprets these.

## Managing Accessibility In Large Teams
But coding this each time in a Blazor app along with the numerous variations is a challenge. If the team is large, you may initially communicate the types of statuses, and describe how only to use a certain role type in design documentation. But those documents may not be read, or someone interprets them in a different way and the bold intention of building accessibility in is drifting away. In small teams its pretty straight forward to enforce design consistency, but as teams get larger it becomes much more challenging.

Creating reusable components solves this problem and makes the design consistently repeatable. Instead of leaving it up to the developer to hand code the numerous attributes and variations. A component can be created which has a range of parameters that can be set when developing. Developers aren’t having to directly code the attributes they're simply setting what type of message to display.

So, lets create an aria live component. I've created the component `AudibleAlert`; it'll use the new WCAG 2.1 alert roles and to maximise compatibility with legacy browsers I'll also add a redundant `aria-live` attribute. We'll use the three roles `log`, `status` and `alert` as a parameter to set what sort of live region we want to create.

When the component is used, we no longer have to manually add the attribute ourselves. We call the component with the role we want as the parameter and what's populated is the correct live region. All we then need to do is wire up how content is added into the live region container. We haven’t had to worry about specifying the correct roles or using the correct attributes, it's all done for us.

But there may be times we want to have more granular control over the type of live region being created. We may want to decide that all updates are announced or only new content and whether to announce additions or deletions of that content. The `AudibleAlert` component can be extended to include extra parameters which can create this variation. It can add the `aria-relevant` attribute to manage which type of updates to be announced, and it can have the `aria-atomic` attribute to specify whether to announce only new updates. And we've chosen easy to understand parameters to reduce the confusion of having to understand the `aria-atomic` or `aria-relevant` attributes.

All you need to then worry about is the trivial way of wiring up the data or values updating the container. This component has provided the accessible scaffolding.

As web applications become more complex, there is a requirement to rely on aria attributes more to provide feedback for users with a range of impairments, but aria attributes are quite brittle. They may not work well in all browsers, and they may not work well in all assistive technologies unless coded correctly. So, taking the responsibility away from the developer to have to worry and think about these sorts of things means a stronger likelihood accessibility is being maintained and is being implemented consistently, the component is called with the parameters and the rest is done automatically.

There is still the flexibility there for developers to choose which statuses are used, and there will probably be mistakes over the incorrect status region chosen for a particular message. But the mistakes now concern if the correct role has been used, not if the correct attributes are used. So, the live region will still work regardless. With components we're moving the accessibility conversation away from is this the right attribute added in the correctly way, to is this the right way the component is being used.

## Managing Focus
Managing focus is crucial to making it easier for keyboard users to navigate around the screen. When modals, or new screens are loaded and the focus isn't programmatically managed and moved to the new content, a keyboard user will have to repeatedly advance the Tab position on to this new content. It's an annoyance for simple screens, but if there's a screen with many controls, or a menu with numerous links that small tab sequence can increase to be in excess of 20 or 30 times a user has to press the Tab key to advance focus.
Programmatically managing the focus means when new content is loaded and we want the user to interact with it, we manually move the screen focus. The user does not have to worry about tabbing to the new content as its already there, and its reduced a potential keyboard accessibility navigation problem.

For this technique we use the Javascript interop feature. This allows us to call Javascript code to interact with the elements on the page and manage focus.

We've created a very trivial bit of Javascript in its own file. We have the sole Javascript function named `IDFocus`, and it accepts an `ID` of any component on the page. The parameter is passed to the Javascript `getElementById()` method and the focus property is called all pretty straightforward stuff. When the function is called with any element ID on the page, the screen focus is positioned onto the element.

The Javascript is called within the Razor component from the `OnAfterRenderAsync` lifecycle method after the control has finished rendering. It has the Javascript function to call along with any parameters to pass in this instance the heading ID but it could equally be any element both interactive and non-interactive. 

If non-interactive elements are being focused those elements then require a `tabindex="-1"` attribute and value to permit programmatic focusing.

I've created a Blazor page called `HeadingFocus.razor`, which for the purposes of this scenario is just having its heading focused. It has a `<h1>` heading element, and because this is a non-interactive element it also has the attribute `tabindex="-1"` to allow programmatic focus. The heading element has an ID which is a set at runtime and this is also the ID passed to the Javascript function.
Within the `OnAfterRenderAsync` method is the call to the Javascript method. 

By applying this technique to all components which trigger the display of new UI content means the screen focus is always in synch with the content. An important point is a similar method must also be created to move the focus back to the calling element when this content is closed or hidden, it’s a two-way process. Move focus to new content when it appears and move focus back to the previous content when it is dismissed.

## In Summary
Native HTML controls as standard have built in semantics which screen reader and other assistive technology can interpret and understand without having to do anything extra. When building your own components, you need to do extra work to bring those controls up to an acceptable level. The `DIV` button is a really good example, deviating from standard controls can become a technical overhead with having to add back features which exist already within HTML elements, and in that case the `BUTTON` element.

Control vendors provide a great range of custom componentry but approach using these with caution. The accessibility statement is often so narrowly defined that the accessibility support just isn't there. Do your due diligence and get the controls tested independently by an external web accessibility company, if in doubt speak to me.

When something changes in a Blazor app, a file is saved or validation errors have occurred and there is a visual indication, provide an audible indication through an aria live region. Aria attributes induce the screen reader to announce updates dynamically. However even though you may build an aria alert to specification, inconsistencies between browsers, and assistive technologies mean it may not work exactly as expected.

Working in large development teams can mean accessibility knowledge becomes diluted and interpreted differently. Leverage the support of components to hide a lot of the granular accessibility details and instead provide parameters which can turn the features on or off. Using components means you're more likely to produce consistent accessibility support, but it does need that initial upfront investment of a dedicated accessibility resource producing the specifications of the components and the expected way they work.

Screen focus needs to be actively managed in Blazor apps. Use Javascript interop to call Javascript methods which focus onto named elements on the page. Managing focus is a two-way process as well, managing it onto a new view when shown, and managing it back to the original calling element when the view is hidden.

Thanks to Adam, Penny and everyone at SSW for running the user group under very difficult circumstances and thanks for the invite.
Blazor is no different than any other framework for accessibility, it doesn’t make any web application any more or less accessible, but you still need to actively put in accessibility support. 

If you're interested in continuing the conversation reach out to me at **ross.mullen@canaxess.com.au** or on Twitter [@MrRossMullen](https://twitter.com/MrRossMullen) and I've put up a repository on GitHub which contains everything I've spoken about as well as links, and the headingfocus and audiblealert pages and component. Go to github.com/canaxess/presentations and navigate to **SSW-Blazor-Accessibility**.

Web accessibility isn't necessarily achieving a 100% WCAG 2.1 accessible score, but its introducing more support for users who navigate in non-traditional ways. 
