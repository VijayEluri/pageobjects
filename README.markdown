
<a href="https://travis-ci.org/stephenh/pageobjects"><img src="https://api.travis-ci.org/stephenh/pageobjects.svg"></a>

A small set of objects to test webapps via selenium.

Example
-------

E.g.:

    public class HomePageObject extends AbstractPageObject {
      public final TextBoxObject username = textBox("username-id");
      public final TextBoxObject password = textBox("password-id");
      public final LinkObject login = link("login-id");

      public HomePageObject(WebDriver d) {
        super(d);
      }
    }

Then in your tests:

    public class HomePageTest {
      // setup a WebDriver instance, e.g. in a common base class
      protected static final WebDriver webDriver = ...
      final HomePageObject home = new HomePageObject(webDriver);

      @Test
      public void testLogin() {
        home.username.type("me");
        home.password.type("password");
        home.login.click();
      }
    }

More Information
----------------

* [Sane AJAX Testing in Selenium](http://draconianoverlord.com/2011/10/14/sane-selenium-testing.html)

Dummy Click Div
---------------

Because Selenium 2.0 closely mimics the browser, just sending keys to a text box will not cause on change to fire. Instead the text box most lose focus, which only happens when another element is selected.

There are a variety of ways to do this (tabbing to the next/previous element for example), but pageobjects uses a "dummy" div that exists solely for switching the focus away from text elements.

This benefit of the dedicated div is that other elements (next/previous) on your page won't be selected as a side-effect by typing in the current element (potentially causing premature validation/etc.).

The downside is that you need an extra element on your page, but it should be easy to put it in a header/footer somewhere and use the following CSS to hide it:

    /* Needs to be onscreen and non-zero size so selenium doesn't complain. */
    /* position:fixed is nicety to avoid selenium jumping around to scroll it into view. */
    /* z-index:1000 puts it in front of any lightbox glass */
    .dummyClickDiv {
      position: fixed;
      top: 0px;
      left: 0px;
      height: 1px;
      width: 1px;
      z-index: 1000;
    }
    <div id="dummy-click-div" class=".dummyClickDiv">&nbsp;</div>

This uses `position: fixed` otherwise if the click div is at the bottom of the page, but a form element is at the top, Selenium will jump back/forth scrolling each into view. This isn't a big deal, just annoying if you're watching the test manually.

**Note:** You can change the dummy click div id by setting the `pageobjects.dummyClickDiv` system property, or calling `DummyClickDiv.setId`. If you set it to `null`, the dummy element logic will be skipped and `shift tab` will be used to change focus instead.

Credits
-------

Heavily inspired by Selenium's [Page Objects](http://code.google.com/p/selenium/wiki/PageObjects), although with a slightly different take.

Download
--------

Available in the [http://repo.joist.ws](http://repo.joist.ws) maven repository, as `com.bizo` `pageobjects` `<version>`.

