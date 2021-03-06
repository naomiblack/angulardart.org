---
layout: tutorial
title: Creating a Custom Component
previous: 04-ch02-controller.html
next: 06-ch04-directive.html
---

# {{page.title}}

<p>In the last chapter, we created a basic Recipe Book app. In this
chapter, we will add to the app by creating a feature that will allow
users to rate each recipe.</p>

<hr />

<h3 id="what-you-will-learn">What you will learn</h3>
<p>Previously, you learned about how Angular implements MVC controllers
through controllers. In this chapter, we will introduce you to
components, which are a second way Angular implements MVC controllers.
When you’re finished, you will understand the similarities and
differences between controllers and components, and you will understand
when to use controllers and when to use components.</p>

<hr class="spacer" />

<h3 id="running-the-sample-app">Running the sample app</h3>
<p>The code for this chapter is in the
<em><a href="https://github.com/angular/angular.dart.tutorial/tree/master/Chapter_03">
  Chapter_03</a></em> directory of the
<a href="https://github.com/angular/angular.dart.tutorial/archive/master.zip">
  angular.dart.tutorial download</a>.
View it in Dart Editor by using
<strong>File &gt; Open Existing Folder...</strong> to open the
<strong>Chapter_03</strong> directory. </p>

<p>Now run the app. In Dart Editor’s Files view, select
<strong>Chapter_03/web/index.html</strong>, right-click, and choose
<strong>Run in Dartium</strong>.</p>

<p>You’ll notice that the rating, which used to be displayed as a boring
number, has now become a not-so-boring cluster of stars. Play around
with the star ratings by changing the rating on each recipe. Click any
of the recipes to display its details, and notice there is also a star
rating component in the recipe details section. Change the recipe rating
in the details section; the rating in the recipe list section also
changes.</p>

<!--
PENDING: We should add screenshots here.
![TEXT GOES HERE](img/ch03-1.png) &nbsp; &nbsp; &nbsp; ![TEXT GOES HERE](img/ch03-2.png)
-->

<hr class="spacer" />

<h3 id="angular-components">Angular components</h3>
<p>The rating feature is an Angular component, implemented using the
<a href="https://docs.angulardart.org/#angular-core-annotation.Component">
  Component</a> annotation. Components are lightweight, reusable,
self-contained UI components that have a single specific purpose. The
rating component is a great example of a small, simple, re-usable
component. It can be used anywhere in your app as many times as you
want. Nothing about the rating component is intrinsically tied to our
Recipe Book. We could use this component in any app to rate anything.</p>

<hr class="spacer" />

<h4 id="using-components-from-your-app">Using components from your app</h4>
<p>Using a component from your app is simple. Just create an HTML element
with the name of the component, and pass any required properties in as
HTML attributes. Here is how our Recipe Book app uses the rating
component.</p>

<script type="template/code">
<rating max-rating="5" rating="ctrl.selectedRecipe.rating"></rating>
</script>

<hr class="spacer" />

<h4 id="creating-components">Creating components</h4>
<p>Creating a component is similar to creating a controller. To create a
component, simply create a class and add the <code>Component</code>
annotation. Here is the annotation for our rating component:</p>

<script type="template/code">
@Component(
    selector: 'rating',
    templateUrl: 'packages/angular_dart_demo/rating/rating_component.html',
    cssUrl: 'packages/angular_dart_demo/rating/rating_component.css',
    publishAs: 'cmp')
class RatingComponent {...}
</script>

<p>Let’s look at those properties in more detail.</p>

<h5 id="selector"><code>selector</code></h5>
<p>The <code>selector</code> property of Component serves the same
purpose as the selector property in a controller. It tells Angular which
CSS selector should trigger the component. By convention, selectors for
components are element names, not attributes (notice the lack of []
chars around the selector). This is because you can only specify one
component on any html element. By contrast, you can specify more than
one controller on an element.</p>

<p>The following code snippet shows the basic differences between how
controllers and components selectors are declared, and how they’re used
in the HTML template.</p>

<!-- PENDING: The following code is a little weird. Make it a table? -->
<script type="template/code">
@Component(
    selector: 'rating',
    ...)

<rating></rating>

@Controller(
    selector: '[recipe-book]',
    ...)

<div recipe-book>
    ...
</div>
</script>

<h5 id="templateurl-and-cssurl"><code>templateUrl</code> and
<code>cssUrl</code></h5>
<p>Since components are self contained, they need to know what HTML
template and CSS to use for their view. Components do not use the HTML
of your app. They have their own.</p>

<h5 id="publishas"><code>publishAs</code></h5>
<p>The <code>publishAs</code> property serves the same purpose as the
<code>publishAs</code> property in a controller. It defines the name
that the view should use to reference the component. Note that the
<code>publishAs</code> name is only visible to the component’s view, not
your app’s view. Your app has no direct visibility into the internals of
the component.</p>

<p>Let’s look at our component’s HTML template:</p>

<script type="template/code">
<span class="stars"
      ng-if="cmp.rating != null"
      ng-repeat="star in cmp.stars"
      ng-click="cmp.handleClick(star)"
      ng-class="cmp.starClass(star)">
  {% raw %}{{cmp.starChar(star)}}{% endraw %}
</span>
</script>

<p><code>rating</code> here refers to the properties and methods on a
RatingComponent object.</p>

<p>A few other properties can be set on a component annotation, but these
are the most important. We will cover some of the less common ones in
later chapters.</p>

<hr class="spacer" />

<h4 id="defining-attributes">Defining attributes</h4>

<p>Recall how our Recipe Book app uses the rating component:</p>

<script type="template/code">
<rating max-rating="5" rating="ctrl.selectedRecipe.rating"></rating>
</script>

<p>To specify which HTML attributes correspond to which properties of the
component, the <code>RatingComponent</code> class uses annotations like the
following:</p>

<script type="template/code">
@NgTwoWay('rating')
  int rating;

@NgAttr('max-rating')
  set maxRating(String value) {...}
</script>

<aside class="alert alert-info">
<b>Note:</b>
NgTwoWay and NgAttr,
along with similar classes,
are deprecated but not yet replaced.
You can view issues related to this effort under the milestone
<a href="https://github.com/angular/angular.dart/issues?milestone=21">bind-*
syntax</a>.
</aside>

<p>The argument to the annotation specifies the HTML attribute name—for
example, “rating” or “max-rating”. Following HTML rules, the attribute
name is case insensitive, with dashes to separate words.</p>

<p>Which annotation you should use depends on how you want the attribute
to be evaluated:</p>

<dl>
<dt> <a href="https://docs.angulardart.org/#angular-core-annotation.NgAttr">NgAttr</a> </dt>
  <dd>
    <p>Sets the property to the value of the attribute, interpolating
      if it contains {% raw %}{{}}{% endraw %} . Our example had this:
      <code>max-rating="5"</code>. You could also get the value from the
      controller by doing something like
      <code>max-rating="{% raw %}{{ctrl.someProperty}}{% endraw %}"</code>.</p>

    <p>
      <code>NgAttr</code> attributes are unidirectional. A copy of the attribute is
      passed to the component, and each instance of the component has
      its own copy. The component can change its local value of the
      property without changing the value outside the component.</p>
  </dd>

<dt>
  <a href="https://docs.angulardart.org/#angular-core-annotation.NgOneWay">
    NgOneWay</a></dt>

<dd>
  <p>Evaluates the attribute's value as an expression and passes the
    result to the component. You can use any valid expression—for
    example, <code>"foo + bar"</code> would pass the result of
    <code>foo + bar</code> to the component.</p>

  <p><code>NgOneWay</code> attributes are unidirectional. The component's property
    changes if the expression's value changes, but changing the
    component's property has no effect outside the component.</p>
</dd>

<dt>
  <a href="https://docs.angulardart.org/#angular-core-annotation.NgTwoWay">
    NgTwoWay</a></dt>

<dd>
  <p>Evaluates the expression, passes the result to the component, and
    keeps the expression and property value in sync. Our example set an
    <code>NgTwoWay</code> attribute using this code:
    <code>rating="ctrl.selectedRecipe.rating"</code></p>

  <p><code>NgTwoWay</code> is bidirectional. When a component changes the value of an
    NgTwoWay-annotated property, the value outside of the component
    changes, as well. In this way, components can change model objects
    in your app. In our example, the rating component changes the rating
    on your RecipeBook’s model objects.</p>
</dd>
</dl>

<hr class="spacer" />

<h4 id="how-is-a-component-different-from-a-controller">How is a component
different from a controller?</h4>

<p>The key difference between components and controllers is that the inner
structure of components are isolated from their surroundings and can be
thought of as black boxes.</p>

<p>Components create their own scope hierarchy that is invisible from the
outside world. They don’t have direct access to the app’s scope, nor
does the app have direct access to a component’s scope.</p>

<p>Components isolate their views from their surroundings by creating a
<a href="http://www.google.com/url?q=http%3A%2F%2Fwww.w3.org%2FTR%2Fshadow-dom%2F&amp;sa=D&amp;sntz=1&amp;usg=AFQjCNF9dpDWirbbvI2JasCRd9Qs1jUg9A">
  shadow DOM</a>. Using a shadow DOM allows components to be used
anywhere without suffering from things like CSS name collisions.</p>

<hr class="spacer" />

<h3 id="angular-features">Angular features</h3>
<p>This chapter introduced you to two more built-in Angular directives:
<code>ng-if</code> and <code>ng-class</code>.</p>

<h4 id="ng-ifhttpciangularjsorgviewdartjobangulardart-masterjavadocangulardirectivengifdirectivehtml">
<a href="https://docs.angulardart.org/#angular-directive.NgIf">
  <code>ng-if</code></a></h4>
<p>The <code>ng-if</code> directive allows you to evaluate sections of the
DOM conditionally. The <code>ng-if</code> directive takes an expression.
If the expression is false, then the portion of the DOM underneath the
if is removed. <code>ng-if</code> does not change the visibility of the
DOM element. It removes it.</p>

<h4 id="ng-classhttpciangularjsorgviewdartjobangulardart-masterjavadocangulardirectivengclassdirectivehtml">
<a href="https://docs.angulardart.org/#angular-directive.NgClass">
  <code>ng-class</code></a></h4>
<p>The <code>ng-class</code> directive allows you to set CSS classes on an
element dynamically using an expression that evaluates to the classes to
be added.</p>