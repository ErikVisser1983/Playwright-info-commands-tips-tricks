## Playwright Documentation
Playwright is a node.js library for Web Testing and Automation.
It allows testing Chromium, Firefox, and WebKit with a single API. Playwright is built to enable cross-browser web automation that is ever-green, capable, reliable, and fast.
It does everything you would expect from the regular test runner.

### 1. Important Features
•	Auto Wait: Waits automatically for controls to be available
•	Scenarios: that span multiple pages, frames, and domain
•	Intercept: Network activity and stubbing, tracing (Check and monitor user behavior on the website)
•	Create an up-to-date, automated testing environment. Run test on the latest version.
•	Emulate: Mobile device, geolocation, and permission
•	Native: Test recorder, Test Runner, Video Capture capability
### 2. Language Binding
The Playwright API is available in multiple languages.
•	JavaScript and TypeScript
•	Python
•	Java
•	.NET
### 3. Installation
Playwright has its own test runner for end-to-end tests, we call it Playwright Test. Playwright is popular for its quick installation.
#### 3.1. Using init command
The easiest way to get started with Playwright Test is to run the init command.
This will create a configuration file, optionally add examples, a GitHub Action workflow, and a first test example.spec.ts. You can now jump directly to writing assertions section.
# Run from your project's root directory
npm init playwright
# Or create a new project
npm init playwright <new-project>
#### 3.2. Manually
Add dependency and install browsers.
npm i -D @playwright/test
# install supported browsers
npx playwright install

You can optionally install only selected browsers, see install browsers for more details. Or you can install no browsers at all and use existing browser channels.
### 4. Requirements
•	NodeJS
•	Visual Studio Code
### 5. Writing Playwright Script
Create tests/sample.spec.js to define your test under the Test folder.
Below test represent multiple things like : 'test' is for Test instance can be used, 'expect' is for Expect is used to add assertions.
'beforeEach' is the hook for test and 'page & browser' are fixer which accessing UI.       

     
const { chromium, test, expect, Page, Browser } = require('@playwright/test');

  test.beforeEach(async ({ page }) => {
    await page.goto(process.env.url);
    await page.locator('text= Clear').first().click();
    await page.locator('#btn_start_assessment').click();
  });
                                                                   
#### 5.1. Execute / Run Code
Command	Description
npx playwright test 	Run your tests, in the tests directory
npx playwright test --headed 	Playwright Test just ran a test using Chromium browser, in a headless manner. Let's tell it to use headed browser:
npx playwright test tests/todo-page.spec.ts 	Run a single test file
npx playwright test tests/todo-page/ tests/landing-page/ 	Run a set of test files
npx playwright test my-spec my-spec-2 	Run files that have my-spec or my-spec-2 in the file name
npx playwright test -g "add a todo item" 	Run the test with the title
npx playwright test --project=firefox 	Run tests in a particular configuration (project)
npx playwright test --workers=1 	Disable parallelization
npx playwright test --reporter=dot 	Choose a reporter

#### 5.2. Configuration file
Playwright Test provides options to configure the default browser, context, and page fixtures. For example, there are options for headless, viewport, and ignoreHTTPSErrors. You can also record a video or a trace for the test or capture a screenshot at the end.
Finally, there are plenty of testing options like timeout or testDir that configure how your tests are collected and executed.
You can specify any options globally in the configuration file, and most of them locally in a test file.
Please read the official documentation for more details.
#### 5.3. Assertions
Playwright Test uses expect library for test assertions. It extends it with the Playwright-specific matchers to achieve greater testing goals.
expect(success).toBeTruthy(); 
Playwright also extends it with convenience async matchers that will wait until the expected condition is met. Consider the following example:
await expect(page.locator('.status')).toHaveText('Submitted'); 
Playwright Test will be re-testing the node with the selector .status until fetched Node has the "Submitted" text. It will be re-fetching the node and checking it over and over, until the condition is met or until the timeout is reached. You can either pass this timeout or configure it once via the testConfig.expect value in test config.
By default, the timeout for assertions is set to 5 seconds. There are multiple assertions. Please see Test Assertions for more details.
#### 5.4. Inspector
Playwright Inspector is a GUI tool that helps to author and debug Playwright scripts. Using this feature you can execute your own code in debug mode when PWDEBUG=1 is set.
$env:PWDEBUG=1
npm run test
Additional useful defaults are configured when PWDEBUG=1 is set:
Browsers launch in the headed mode
The default timeout is set to 0 (= no timeout)

Also by clicking on the record button, we can record our activity and Playwright will generate code in the inspector window in the language we have selected. 
Use open or codegen commands in the Playwright CLI:
npx playwright codegen wikipedia.org
At any moment, clicking the Record action enables codegen mode. Every action on the target page is turned into the generated script.
#### 5.5. Using test hooks
You can use test.beforeAll and test.afterAll hooks to set up and tear down resources shared between tests. And you can use test.beforeEach and test.afterEach hooks to set up and tear down resources for each test individually.

 test.afterAll(async ({ browser }) => {
    await browser.close();
  });
#### 5.6. Reports
Playwright Test comes with a few built-in reporters for different needs and the ability to provide custom reporters. The easiest way to try out built-in reporters is to pass the --reporter  command-line option.
npx playwright test --reporter=line
For more control, we can specify reporters programmatically in the configuration file.
In the current project, I used HTML reports.
HTML reporter produces a self-contained folder that contains the report for the test run that can be served as a web page.
By default, the HTML report is opened automatically if some of the tests failed. You can control this behavior via the open property in the Playwright config. The possible values for that property are always, never, and on-failure (default).
const config = {
  reporter: [ ['html', { open: 'never' }] ],
};

module.exports = config;
By default, the report is written into the playwright-report folder in the current working directory. One can override that location using the PLAYWRIGHT_HTML_REPORT environment variable or a reporter configuration.

In the configuration file, pass options directly:
const config = {`
  reporter: [ ['html', { outputFolder: 'my-report' }] ],
};

module.exports = config;
A quick way of opening the last test run report is:
npx playwright show-report
Or if there is a custom folder name:
npx playwright show-report my-report

#### 5.7. How to Run Playwright Tests Sequentially in Same Browser Context

If you have worked with the Playwright framework you might have observed that if you write multiple tests inside the describe function, it gets executed one after another but each test runs in a separate browser context. That means that if you have performed a login in test1() it doesn't preserve those in test2(). The reason is that each time playwright executes the test, it creates a new context and executes the test. This feature also allows you to write independent tests and you can execute them parallelly with less execution time. Also its taking configuration from playwright.config file for browser context. For Ex: 

const { chromium, test, expect, Page, Browser } = require('@playwright/test');

test.describe('test',async()=>{
  test('Navigate to Google', async ({page}) => {
    await page.goto('https://google.com/');
    const url=await page.url();
    expect(url).toContain('google');
  });

  test('Search for Playwright', async ({page}) => {
    await page.type('input[name="q"]',"Playwright")
    await page.keyboard.press('Enter');
    let text=await page.innerText('//h3[contains(text(),"Playwright:")]')
    expect(text).toContain('Playwright: Fast and reliable');
  });
});

In the above code, Both of the tests run in a separate context since we are passing {page} as an argument to the test function. That's why above code wont work as we are assuming that it will navigate first and then we can search in second test. However for second case URL is not mentioned so that will fail.  
If we are looking for a solution like scenarios inside the Playwright describe block, you should execute one after another in the browser. By preserving the previous state, we can modify your tests below.

const { chromium, test, expect, Page, Browser } = require('@playwright/test');

test.describe('test', async () => {
  let page = Page;
  test.beforeAll(async ({ browser }) => {
    page = await browser.newPage();
  });

  test('Navigate to Google', async () => {
    await page.goto('https://google.com/');
    const url = await page.url();
    expect(url).toContain('google');
  });

  test('Search for Playwright', async () => {
    await page.type('input[name="q"]', "Playwright")
    await page.keyboard.press('Enter');
    let text = await page.innerText('//h3[contains(text(),"Playwright:")]')
    expect(text).toContain('Playwright: Fast and reliable');
  });
});
If you look at the above code, the test() doesn't have {page} context passed as an argument here, so it takes from the page which we have initiated under the beforeAll(). With the above approach, your tests run just like any other framework code in the same browser context. Second test will be run as browserContext has URL already.
Note : Page and Browser will get default config for ex : Video and Screenshot 'OFF', it wont follow playwright.config. We can override default config while declaring browser context but test evidences will be stored in different location so wont reflect in Test report. 

How to override browserContext config : Browser Context Setting 

Environment Set Up:
Few things needs to be done before executing the Test which are mentioned as below:
•	Add the Environment Variables in PC >> Advance system settings >> System Properties >> Environment Variables 
o	Click on 'New' button to add a User Variable
o	Set the Variable as "secretSaltBbs" and Value as "TestBbs"
o	Set the Variable as "url" and Value as "https://localhost:7216/" 

 
 

