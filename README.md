# GBC NJM Customisation

NOTE: The GBC customization was done using the 1.00.53 release.


## Folders
* Screenshots : The screenshots for this README
* gbc-njm : The files for the custom GBC
* distbin : place for zip of GBC


## Building
This was written and tested on Linux. The Makefile and Script are to setup additional folders and to build the GBC.
* 1. The GBC prerequisites building ( grunt nodejs git ) plus: make & zip installed
* 2. Set GBCPROJECTDIR=<folder containing fjs-gbc-1.00.53-build201905131540-project.zip>
 
```
$ git clone <repo url>
$ cd <folder cloned>
$ make
```

## Screenshots
![ss1](https://github.com/neilm-fourjs/gbc_njm/raw/master/Screenshots/SS-1.png "SS1")


## GBC Customizations - CSS

### Customized the colours ( theme.scss.json )

See GBC manual for the theme colour details

### Removed the sidebar ( theme.scss.json )

See GBC manual for the 'gbc-sidebar-always-visible-min-width' values

### Removed the applicationHostMenu ( ApplicationHostWidget.scss )

If you want to turn off the default menu bar completely you can use this:
```css
.gbc_ApplicationHostWidget {
  header.mt-toolbar {
      display: none;
  }
}
```

### Removed the next/previous image space from the folder headings & toolbar ( main.scss )

Folders and toolbar can have next/previous images, but even when not required there is space for those image which can look bad.
```css
.vanished {
  width: 1px;
}
```

### Re-styled the window title bar for modal windows ( MyDialogWindowHeading.scss )

The window title was subtle,  I felt it needed to stand out as a title, so I restyled it.
```css
.gbc_ModalWidget .mt-dialog-header .mt-dialog-title {
  padding-top: 1px;
  fill: $gbc-secondary-text-color;
}

.gbc_ModalWidget .mt-dialog-header .mt-dialog-actions {
  color: $gbc-secondary-text-color;
  fill: $gbc-secondary-text-color;
}

.gbc_ModalWidget .mt-dialog-header {
  background-color: $gbc-primary-medium-color;
  color: $gbc-secondary-text-color;
  fill: $gbc-secondary-text-color;
  padding: 5px 2px 2px 5px;
  margin-bottom: 5px;
}

```

### Table headers to use gbc-primary-light-color for color ( MyTableWidget.scss )

The default table headers were a little bland, so I re-styled them.
```css
.gbc_TableWidget .gbc_TableColumnsHeaders, .gbc_TableWidget .gbc_TableLeftColumnsHeaders, .gbc_TableWidget .gbc_TableRightColumnsHeaders {
        background-color: $theme-primary-background-color;
        color: $palette-text-primary;
}
```

## GBC Customizations - Javascript/HTML

### Header text / logo ( MyHeaderBarWidget )
The header title and the logo were done using the method outlined in the GAS manual.
The header MyHeaderBarWidget.tpl.html file was expended to have a table to align the logo/title/app counter

```html
<div>
	<table width="100%">
		<tr>
			<td id="logocell" width="20%" align="left">
				<img src="./resources/img/logo_light.png"/>
			</td>
			<td width="60%">
				<h1 class="MyHeaderBarWidget-banner"></h1>
				<b id="MyWinTitle" class="MyHeaderBarWidget-title"></b>
			</td>
			<td width="20%" align="right">
				<b id="dyntext">none</b>
				<span style="display: none">Apps: <b class="MyHeaderBarWidget-counter"></b></span>
			</td>
		</tr>
	</table>
</div>
```

The header shows the application title and the window, which will change as you open new windows.
```javascript
        onCurrentWindowChanged: function(windowNode) {
          var elt = this.getElement().querySelector(".MyHeaderBarWidget-title");
          // Set the banner text to the value set by ui.interface.setText()
          elb.textContent = windowNode.getAncestor("UserInterface").attribute('text');
          // Set the header sub title to the window text.
          if (windowNode) {
            elt.textContent = windowNode.attribute('text');
          } else {
            elt.textContent = "<NONE>";
          }
        }
```

### Footer to be at bottom of page rather than bottom of window ( MyFormWidget )

Here the goal was a footer that was at the bottom of the web page rather then anchored to the bottom of the browser window.
So I only wanted to see the footer when I scroll all the way down.


To make this work the footer has to be part of the 'form'. I created a MyFormWidget.tpl.html of this:
```html
<div>
  <div class="scroller">
    <div class="containerElement">
      <footer>
This is my customized GBC Demo - by neilm@4js.com
      </footer>
    </div>
  </div>
</div>
```
So the Genero 'form' will replace the 'containerElement' and below that will be the 'footer'.
Next we need to create the MyFormWidget.js file to use this tpl.html file.
```javascript
modulum('MyFormWidget', ['FormWidget', 'WidgetFactory'],
  function(context, cls) {
    cls.MyFormWidget = context.oo.Class(cls.FormWidget, function($super) {
      return {
        __name: "MyFormWidget"
      };
    });
    // register the class so only forms with a style of 'gbc_footer' use this widget.
    cls.WidgetFactory.register('Form', 'gbc_footer', cls.MyFormWidget);
});
```
You can see the MyFormWidget.js doesn't actually 'do' anything - it's inheriting all the methods from the default 'FormWidget' and overriding nothing. 
The class is registered with a style, so only a Form with a style of 'gbc_footer' will get the footer - otherwise all forms would get the footer.

### Change the 'End Session' page ( MySessionEndWidget.js )

See GBC Manaul

