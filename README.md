
# angular2-virtual-scroll

Virtual Scroll displays a virtual, "infinite" list. Supports multi-column.

## About

This module displays a small subset of records just enough to fill the viewport and uses the same DOM elements as the user scrolls.
This method is effective because the number of DOM elements are always constant and tiny irrespective of the size of the list. Thus virtual scroll can display an infinitely growing list of items in an efficient way.

* Angular 4+ compatible module
* Supports multi-column
* Easy to use apis
* OpenSource and available in GitHub

## Breaking Changes:
* The value of ChangeEvent.end wasn't intuitive. This has been corrected. Both ChangeEvent.start and ChangeEvent.end are the 0-based array indexes of the items being rendered in the viewport. (Previously Change.End was the array index + 1)

## New features:

* Added ability to put other elements inside of scroll (Need to wrap list itself in @ContentChild('container'))
* Added ability to use any parent with scrollbar instead of this element (@Input() parentScroll)
* Added ability to use horizontal scrollbars
* New feature is support of elements with different heights *EXPERIMENTAL*
* If scroll container resizes, the items will auto-refresh. Can be disabled if it causes any performance issues by setting [checkResizeInterval]="0"
* useMarginInsteadOfTranslate flag. Defaults to false. This can affect performance (better/worse depending on your circumstances), and also creates a workaround for the transform+position:fixed browser bug.
 
## Demo

[See Demo Here](http://rintoj.github.io/angular2-virtual-scroll)

## Usage

```html
<virtual-scroll [items]="items" (update)="viewPortItems = $event">

    <my-custom-component *ngFor="let item of viewPortItems">
    </my-custom-component>

</virtual-scroll>
```

alternatively

```html
<virtual-scroll #scroll [items]="items">

    <my-custom-component *ngFor="let item of scroll.viewPortItems">
    </my-custom-component>

</virtual-scroll>
```

alternatively

```html
<div virtualScroll [items]="items" (update)="viewPortItems = $event">

    <my-custom-component *ngFor="let item of viewPortItems">
    </my-custom-component>

</div>
```

## Get Started

**Step 1:** Install angular2-virtual-scroll

```sh
npm install angular2-virtual-scroll --save
```

**Step 2:** Import virtual scroll module into your app module

```ts
....
import { VirtualScrollModule } from 'angular2-virtual-scroll';

....

@NgModule({
    ...
    imports: [
        ....
        VirtualScrollModule
    ],
    ....
})
export class AppModule { }
```

**Step 3:** Wrap virtual-scroll tag around elements;

```ts
<virtual-scroll [items]="items" (update)="viewPortItems = $event">

    <my-custom-component *ngFor="let item of viewPortItems">
    </my-custom-component>

</virtual-scroll>
```

You must also define width and height for the container and for its children.

```css
virtual-scroll {
  display: block;
  width: 350px;
  height: 200px;
}

my-custom-component {
  display: block;
  width: 100%;
  height: 30px;
}

```

**Step 4:** Create 'my-custom-component' component.

'my-custom-component' must be a custom angular2 component, outside of this library.

Child component is not necessary if your item is simple enough. See below.

```html
<virtual-scroll [items]="items" (update)="viewPortItems = $event">
    <div *ngFor="let item of viewPortItems">{{item?.name}}</div>
</virtual-scroll>
```

## API

| Attribute      | Type   | Description
|----------------|--------|------------
| enableUnequalChildrenSizes | boolean | If you want to use the "unequal size" children feature. This is not perfect, but hopefully "close-enough" for most situations. Defaults to false.
| useMarginInsteadOfTranslate | boolean | Defaults to false. Translate is faster in many scenarios because it can use GPU acceleration, but it can be slower if your scroll container or child elements don't use any transitions or opacity. More importantly, translate creates a new "containing block" which breaks position:fixed because it'll be relative to the transform rather than the window. If you're experiencing issues with position:fixed on your child elements, turn this flag on.
| scrollbarWidth | number | If you want to override the auto-calculated scrollbar width. This is used to determine the dimensions of the viewable area when calculating the number of items to render.
| scrollbarHeight | number | If you want to override the auto-calculated scrollbar height. This is used to determine the dimensions of the viewable area when calculating the number of items to render.
| horizontal | boolean | Whether the scrollbars should be vertical or horizontal. Defaults to false.
| items          | any[]  | The data that builds the templates within the virtual scroll. This is the same data that you'd pass to ngFor. It's important to note that when this data has changed, then the entire virtual scroll is refreshed.
| childWidth     | number | The minimum width of the item template's cell. Use this if enableUnequalChildrenSizes isn't working well enough. (The actual rendered size of the first cell is used by default if not specified.)
| childHeight    | number | The minimum height of the item template's cell. Use this if enableUnequalChildrenSizes isn't working well enough. (The actual rendered size of the first cell is used by default if not specified.)
| bufferAmount   | number | The the number of elements to be rendered above & below the current container's viewport. Useful when not all elements are the same dimensions. (defaults to enableUnequalChildrenSizes ? 5 : 0)
| scrollAnimationTime | number | The time in milliseconds for the scroll animation to run for. Default value is 750. 0 will completely disable the tween/animation.
| parentScroll   | Element / Window | Element (or window), which will have scrollbar. This element must be one of the parents of virtual-scroll
| start         | Event  | This event is fired every time `start` index changes and emits `ChangeEvent` which of format: `{ start: number, end: number }`
| end         | Event  | This event is fired every time `end` index changes and emits `ChangeEvent` which of format: `{ start: number, end: number }`
| update         | Event  | This event is fired every time the `start` or `end` indexes change and emits the list of items which should be visible based on the current scroll position from `start` to `end`. The list emitted by this event must be used with `*ngFor` to render the actual list of items within `<virtual-scroll>`
| change         | Event  | This event is fired every time the `start` or `end` indexes change and emits `ChangeEvent` which of format: `{ start: number, end: number }`

## Getting view port items without events

If you are using AOT compilation (I hope you are) then with classic usage (listening to `update` event) you are required to create a public field `viewPortItems` in your component.
Here's a way to avoid it:
```html
<virtual-scroll #scroll [items]="items">

    <my-custom-component *ngFor="let item of scroll.viewPortItems">
    </my-custom-component>

</virtual-scroll>
```

## Additional elements in scroll

If you want to nest additional elements inside virtual scroll besides the list itself (e.g. search field), you need to wrap those elements in a tag with an angular selector name of #container.

```html
<virtual-scroll [items]="items"
    (update)="viewPortItems = $event">
    <input type="search">
    <div #container>
        <my-custom-component *ngFor="let item of viewPortItems">
        </my-custom-component>
    </div>
</virtual-scroll>
```

## Use parent scrollbar

If you want to use the scrollbar of a parent element, set `parentScroll`.

```html
<div #scrollingBlock>
    <virtual-scroll [items]="items"
        [parentScroll]="scrollingBlock.nativeElement"
        (update)="viewPortItems = $event">
        <input type="search">
        <div #container>
            <my-custom-component *ngFor="let item of viewPortItems">
            </my-custom-component>
        </div>
    </virtual-scroll>
</div>
```

Note: The parent element should have a width and height defined.

## Use scrollbar of window

If you want to use the window's scrollbar, set `parentScroll`.

```html
<virtual-scroll
    #scroll
    [items]="items"
    [parentScroll]="scroll.window">
    <input type="search">
    <div #container>
        <my-custom-component *ngFor="let item of scroll.viewPortItems">
        </my-custom-component>
    </div>
</virtual-scroll>
```

## Items with variable size

Items must have fixed height and width for this module to work perfectly. However if you have items with variable width and height, set inputs `childWidth` and `childHeight` to their smallest possible values. You can also modify `bufferAmount` which causes extra items to be rendered on the edges of the scrolling area.
If you are unsure what values to use for `childWidth` and `childHeight` and `bufferAmount` you can instead set [enableUnequalChildrenSizes]="true". This will cause all 3 values to be auto-calculated.



```html
<virtual-scroll [items]="items"
    [childWidth]="80"
    [childHeight]="30"
	[bufferAmount]="5"
    (update)="viewPortItems = $event">

    <my-custom-component *ngFor="let item of viewPortItems">
    </my-custom-component>

</virtual-scroll>
```

or

```html
<virtual-scroll [items]="items"
    [enableUnequalChildrenSizes]="true"
    (update)="viewPortItems = $event">

    <my-custom-component *ngFor="let item of viewPortItems">
    </my-custom-component>

</virtual-scroll>
```

## Loading in chunks

The event `end` is fired every time the scrollbar reaches the end of the list. You could use this to dynamically load more items at the end of the scroll. See below.

```ts

import { ChangeEvent } from 'angular2-virtual-scroll';
...

@Component({
    selector: 'list-with-api',
    template: `
        <virtual-scroll [items]="buffer" (update)="scrollItems = $event"
            (end)="fetchMore($event)">

            <my-custom-component *ngFor="let item of scrollItems"> </my-custom-component>
            <div *ngIf="loading" class="loader">Loading...</div>

        </virtual-scroll>
    `
})
export class ListWithApiComponent implements OnChanges {

    @Input()
    items: ListItem[];

    protected buffer: ListItem[] = [];
    protected loading: boolean;

    protected fetchMore(event: ChangeEvent) {
        if (event.end !== this.buffer.length-1) return;
        this.loading = true;
        this.fetchNextChunk(this.buffer.length, 10).then(chunk => {
            this.buffer = this.buffer.concat(chunk);
            this.loading = false;
        }, () => this.loading = false);
    }

    protected fetchNextChunk(skip: number, limit: number): Promise<ListItem[]> {
        return new Promise((resolve, reject) => {
            ....
        });
    }
}
```

## If container size changes

Note: This should now be auto-detected, however the 'refresh' method can still force it if neeeded.
	This was implemented using the setInterval method which may cause minor performance issues. It shouldn't be noticeable, but can be disabled via [checkResizeInterval]="0"
	Performance will be improved once "Resize Observer" (https://wicg.github.io/ResizeObserver/) is fully implemented.

Refresh method (DEPRECATED)
If virtual scroll is used within a dropdown or collapsible menu, virtual scroll needs to know when the container size changes. Use `refresh()` function after container is resized (include time for animation as well).

```ts
import { Component, ViewChild } from '@angular/core';
import { VirtualScrollComponent } from 'angular2-virtual-scroll';

@Component({
    selector: 'rj-list',
    template: `
        <virtual-scroll [items]="items" (update)="scrollList = $event">
            <div *ngFor="let item of scrollList; let i = index"> {{i}}: {{item}} </div>
        </virtual-scroll>
    `
})
export class ListComponent {

    protected items = ['Item1', 'Item2', 'Item3'];

    @ViewChild(VirtualScrollComponent)
    private virtualScroll: VirtualScrollComponent;

    // call this function after resize + animation end
    afterResize() {
        this.virtualScroll.refresh();
    }
}
```

## Focus an item

You can use the `scrollInto(item, alignToBeginning?, additionalOffset?, animationMilliseconds?, animationCompletedCallback?)` api to scroll into an item in the list.
You can also use the `scrollToIndex(index, alignToBeginning?, additionalOffset?, animationMilliseconds?, animationCompletedCallback?)` api for the same purpose.
See below:

```ts
import { Component, ViewChild } from '@angular/core';
import { VirtualScrollComponent } from 'angular2-virtual-scroll';

@Component({
    selector: 'rj-list',
    template: `
        <virtual-scroll [items]="items" (update)="scrollList = $event">
            <div *ngFor="let item of scrollList; let i = index"> {{i}}: {{item}} </div>
        </virtual-scroll>
    `
})
export class ListComponent {

    protected items = ['Item1', 'Item2', 'Item3'];

    @ViewChild(VirtualScrollComponent)
    private virtualScroll: VirtualScrollComponent;

    // call this function whenever you have to focus on second item
    focusOnAnItem() {
        this.virtualScroll.items = this.items;
        this.virtualScroll.scrollInto(items[1]);
    }
}
```

## Sorting Items

Always be sure to send an immutable copy of items to virtual scroll to avoid unintended behavior. You need to be careful when doing non-immutable operations such as sorting:

```ts
sort() {
  this.items = [].concat(this.items || []).sort()
}
```

## Contributing
Contributions are very welcome! Just send a pull request. Feel free to contact me or checkout my [GitHub](https://github.com/rintoj) page.

## Author

* **Rinto Jose** (rintoj)
* **Pavel Kukushkin** (kykint)

### Hope this module is helpful to you. Please make sure to checkout my other [projects](https://github.com/rintoj) and [articles](https://medium.com/@rintoj). Enjoy coding!

Follow me:
  [GitHub](https://github.com/rintoj)
| [Facebook](https://www.facebook.com/rinto.jose)
| [Twitter](https://twitter.com/rintoj)
| [Google+](https://plus.google.com/+RintoJoseMankudy)
| [Youtube](https://youtube.com/+RintoJoseMankudy)

## Versions
[Check CHANGELOG](https://github.com/rintoj/angular2-virtual-scroll/blob/master/CHANGELOG.md)

## License
```
The MIT License (MIT)

Copyright (c) 2016 Rinto Jose (rintoj)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
