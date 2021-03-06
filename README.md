# mtaf
Mobile Test Automation Framework
--------------------------------

The Mobile Test Automation Framework (MTAF) supports end-to-end regression
tests of web and Android applications. It provides unit test and
Behavior-Driven Development (BDD) test case organization using a Python Page
Object Model architecture. User interaction with the application under test
(AUT) is simulated using Selenium Webdriver (for web applications) and the
Appium Python Client (for Android applications). Support for iOS (also using
Appium) is planned for future development.

The first release of the MTAF includes only the MTAF Inspector, a tool to
help test programmers create automated tests that use Appium to interact with
Android applications. The remaining framework components will be included
once they have been refactored for general release.

MTAF Inspector creates a graphical user interface (GUI) to capture application
screenshots and calculate unique partial xpaths for displayed elements. The
resulting xpaths are converted to an abbreviated format called "zpath" to make
them shorter (Android xpaths can be quite long).

The user can select elements individually with a mouse click, or click and
drag a selection rectangle on the displayed screenshot to narrow down the
list of partial zpaths shown on the screen. Resource ID's are also displayed,
but some elements won't have one, and a resource ID may not represent a unique
element on the screenshot.

Clicking on an ID or xpath will outline the corresponding element(s) on the
screenshot, and copy the ID or zpath value to the main GUI.

The main GUI can use Appium to find elements matching the ID or zpath
value, and show information about the elements.

----

Features
========
**MTAF Inspector:**
    - Graphical tool to assist in designing end-to-end tests on Android devices
    - Works with Android device emulators, or Android devices via USB connection
    - Interactively determines valid locators for screen elements, for use with
      Appium
    - When no Appium server is present, grabs screenshots and xml using adb
    - When used with Appium server, finds and manipulates visible screen
      elements
    - Records Appium interactions in a log file
    - Loads plugins to extend menus, zpaths and GUI elements for specific
      applications

**MTAF Library Modules (Python):**
    - ADB
        For using ADB from Python, the pyand (https://github.com/ardevd/pyand)
        ADB module is included (which avoids the problem of "pip install" not
        recognizing modules hosted on github)
    - selenium_actions
        Defines SeleniumActions class for using Selenium methods
    - android_actions
        Defines AndroidActions class for using Appium Python Client methods
    - ios_actions (future)
        Defines IosActions class for using Appium Python Client methods
    - android_zpath
        Defines abbreviations used to convert Android xpaths to zpaths, a
        short format that makes working with locators a lot easier
    - ios_zpath (future)
        Defines abbreviations used to convert iOS xpaths to zpaths
    - filters.py
        Filter functions used to narrow down lists of screen elements returned
        by the Appium Python Client search methods
    - mtaf_logging
        Wrapper for Python logging
    - prune_logs
        Utility to limit number and/or age of timestamped logs retained
    - trace
        Decorators for logging method calls and return values
    - user_exception
        Defines UserException class for graceful exception handling in test code
    - softphone (future)
        Creates and manages SIP softphones to make and receive calls for testing
        devices that support voice calls

**Page Object Model (future):**
    - Each visible view (called a "page") displayed by an application under test
      has a Python class which contains locators for that page, and methods to
      be called while that page is displayed
    - Locators for visible elements on the page are given names that convey the
      element's function
    - Each page class represents a separate namespace for locator names, so that
      names can be re-used on other pages without conflict
    - Test steps are generally implemented by calling methods belonging to the
      current page's class, and these methods obtain locators from the locator
      namespace belonging to their class
    - Pages can inherit from a common base page class when they share common
      elements and functionality; in this case, methods and locators are
      inherited from the base class unless overridden in the current page class

**Python Unit Test (future):**
    - Test suite classes are structured using the Python unittest module
      conventions
    - Custom logging, and method call tracing using decorators

**Python Feature Test (future):**
    - Uses the Python "behave" framework
    - Tests are written in "feature" files, using Gherkin BDD language
    - Feature files describe tests in feature/scenario/step format, using "plain language" phrases to describe user interaction with the application
    - Test step phrases are implemented in Python
    - Test results are saved in a MongoDB database and available for review using a web browser

**Report Single Page Application (future):**
    - AngularJS application displays test results saved by Python Feature Test

----

Running Inspector
=================

Once mtaf has been installed, Inspector can be run from a script if the
following requirements are met:

- adb can be found on the current path
- Python and Tkinter are installed on the local machine
- an Appium server is running on the local machine (optional)
- the script has permission to write in the temporary files directory (default value is '.MtafInspector' in the user's home directory)

Inspector can be started with these two Python script lines::

    from mtaf import start_inspector
    start_inspector()

Or, if the mtaf package is installed from PyPi (using "pip install mtaf"), the executable script "mtaf-inspector" will be available on the user's path.

Inspector presents a GUI with these components (from top to bottom):

- a menu bar with drop-down menus for both Appium and ADB operations
- buttons and other controls for performing various operations that require Appium
- a input field for entering arbitrary Python commands that will be run in the global context when the "exec" button is clicked
- a scrolling text window that displays captured standard output
- a scrolling text window that displays recorded commands
- a bar with "screenshot" and "quit" buttons

The buttons and controls requiring Appium, along with the Appium drop-down menu,
are disabled until Appium is started. To start Appium, run an Appium server
instance on the local machine, and click "Start Appium" on the menu
bar.

Click the "screenshot" button to capture a screenshot and the xml
representation of the current display.  This does not require Appium to be
started, since Inspector will use ADB if Appium has not been started.
Inspector will display the screenshot, and a column of minimum unique relative
zpaths for each element on the display; these are translated by MTAF methods
into the shortest xpaths that Appium can use to find that individual element.
Some elements may have resource id's, which will be
displayed in a separate column. Clicking a resource id will highlight one or
more elements on the screenshot (preloading the "find element" locator field in
the main GUI), and corresponding zpaths will be highlighted. Clicking a zpath
will highlight one element (highlighting the corresponding resource ID if the
element has one) and preload the zpath into the "find element" locator field.
