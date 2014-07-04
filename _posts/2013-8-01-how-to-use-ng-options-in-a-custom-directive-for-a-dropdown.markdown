---
layout: post
title:  "How to use ng-options in a custom directive for a dropdown"
date:   2013-08-01 19:55:16
categories: angular
---

Sometimes you want to style select boxes in a way that requires a few extra HTML tags, like this:

<center><img src="{{ site.baseurl }}/images/posts/dropdown-angular-directive.png"/></center>

While I’m not going into detail for the CSS behind the visuals, I will outline how you can build a directive to give you the proper HTML to build such a visual element.

Doing this kind of thing can be a little tricky with [AngularJS](https://angularjs.org/) because you’ll still want to properly utilize `ng-options`.  The directive `ng-options` is terminating, which means that no other directives can be executed after it has.  If you use ng-options on your own directive, say `<select-box ng-options=”my.options”>` you can’t use any directives within `<select-box>`‘s template.

To properly mirror the select box functionality, we’ll need to do things a little differently. We’ll add a few extra attributes:
 
 * `options` – an array of data used to populate the dropdown
 * `optExp` – the expression used to read the options. Note that this expression should be reading values from options
 
 **Usage:**
 
 ```html
 <select-box name="demo" ng-model="myValue" options="myOptions" optExp="t.name for t in options | orderBy:'name'" defaultLabel="Select One"></select-box>
 ```
 
 **The directive:**
 
 ```js
 directive('selectBox', function () {
     return {
         replace: true,
         restrict: 'E',
         scope: false,
         template: function (element, attrs) {
             if (!angular.isDefined(attrs.defaultLabel))
                 attrs.defaultLabel = "";
 
             return '<div class="selectBox selector">'+
                         '<span>{{ ngModel.name || "' + attrs.defaultLabel + '"}}</span>'+
                         '<select name="' + attrs.name + '" ng-model="' + attrs.ngModel + '" ng-options="' + attrs.optexp + '"' + ((attrs.required) ? ' required' : '') + '></select>'+
                    '</div>';
         },
         link: function (scope, el, attrs) {
             scope.$watch(attrs.ngModel, function () {
                 var model = scope.$eval(attrs.ngModel);
                 //when value changes, update the selectBox text
                 if (angular.isDefined(model) && angular.isDefined(model.name)) {
                     el[0].firstChild.innerText = model.name;
                 }
             });
         }
     }
 })
 ```
 
 View this code live in a plunker here: [http://plnkr.co/edit/nQbrSi](http://plnkr.co/edit/nQbrSi)
 
 Requires AngularJS v1.1.4+