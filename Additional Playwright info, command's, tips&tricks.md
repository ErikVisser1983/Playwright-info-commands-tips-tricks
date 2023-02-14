## Playwright aanvullende documentatie
### 1. Belangrijke Features
•	Auto Wait: Wacht automatisch voor controle punten anders geeft het een time-out error terug - https://playwright.dev/docs/actionability
•	Scenarios: test over verschillende browsers of tabs in één browser - https://playwright.bootcss.com/python/docs/multi-pages 
•	Intercept: Netwerk activiteit en stubs/mocks - https://timdeschryver.dev/blog/intercepting-http-requests-with-playwright#intercept-a-request-to-return-mocked-response 
•	Configureer en draai een test omgeving - https://playwright.dev/docs/test-advanced#configuration-object 
•	Emulatie: Mobiele devices, geolocatie en rechten - https://playwright.dev/docs/emulation
•	Native: Test recorder, Test Runner, Video Capture capability
### 2. Language Binding
De Playwright API is beschikbaar in verschillende talen
•	JavaScript and TypeScript
•	Python
•	Java
•	.NET
### 3. Installatie
Playwright heeft zijn eigen 'test runner' voor end-to-end tests, ze noemen het Playwright Test. 
#### 3.1. Gebruik van het init command
Een van de makkelijkere manieren om te starten met Playwright Test is door het uitvoeren van het init command.
Dit zal een configuration file, optinele voorbeelden, een GitHub Action workflow, en een eerste test example.spec.ts crieëren. 
# Draai vanuit je project root folder
npm init playwright
# Of crieereen nieuw project
npm init playwright <new-project>
#### 3.2. Manueel
Next npm Dist Tag
npm i -D @playwright/test - https://playwright.dev/docs/canary-releases#next-npm-dist-tag
# install supported browsers
npx playwright install -https://playwright.dev/docs/library#key-differences
Je kan optioneel alleen opgegeven browsers installeren. 
### 4. Requirements
•	NodeJS
•	Visual Studio Code
### 5. Maak een Playwright Script
Maak een test/smaple.spec.js om je test te definieren onder je test folder
De voorbeeld test hieronder toont meerdere elementen als: 'test' staat voor de Test instance dat gebruikt mag worden, 'expect' is voor Expect om controle punten (assertions) toe te kunnen voegen.
Het gebruik van de 'beforeEach' functie - https://playwright.dev/docs/test-annotations#use-fixme-in-beforeeach-hook 

const { chromium, test, expect, Page, Browser } = require('@playwright/test');

  test.beforeEach(async ({ page }) => {
    await page.goto(process.env.url);
    await page.locator('text= Clear').first().click();
    await page.locator('#btn_start_assessment').click();
  });
                                                                   
#### 5.1. Execute / Run Code
- npx playwright test -- Draai je tests in de test directory
- npx playwright test --headed -- Vertelt Playwright om in een headed browser te draaien. Meer over headless browsers vind je hier https://oxylabs.io/blog/what-is-headless-browser
- npx playwright test tests/todo-page.spec.ts -- Draai een enkele test file
- npx playwright test tests/todo-page/ tests/landing-page/ -- Draai een set van test files
- npx playwright test my-spec my-spec-2 -- Draai tests met die de naam  my-spec of my-spec-2 in de bestandsnaam bevatten
- npx playwright test -g "add a todo item" -- Draai de test welke de volgende titel bevat
- npx playwright test --project=firefox -- Draai alle tests tegen een specifiek project
- npx playwright test --workers=1 -- Schakel parallelizatie uit
- npx playwright test --reporter=dot -- Kies een reporter, je vind hier https://playwright.dev/docs/test-reporters meer over reporters

#### 5.2. Configuratie 
Playwright Test voorziet opties om de default browser te configureren, context en pagina fixtures. 
Als voorbeeld, er zijn opties for headless, viewport, en negeer HTTPS Errors.  
- https://playwright.dev/docs/api/class-testoptions#test-options-ignore-https-errors

Ook kan je een video openemen,een trace uitvoeren tijdens de test of een screenshot maken aan het einde.
- https://playwright.dev/docs/videos#record-video 
- https://playwright.dev/docs/trace-viewer#screenshots

Er zijn ook test opties zoals timeouts of testDir configuratie waarmee je de wijze waarop je testen verzamelt uit uitgevoerd worden  
- https://playwright.dev/docs/test-timeouts
- https://playwright.dev/docs/api/class-testproject#test-project-test-dir

#### 5.3. Assertions
Playwright Test gebruikt de 'expect' bibliotheek voor test controles (assertions) - https://playwright.dev/docs/test-assertions
b.v.  expect(success).toBeTruthy(); 
Om de testen nog robuuster te maken beschikt Playwright ook over wat zij noemen 'convenience async matchers'. 
Twee voorbeelden:
- expect(locator).toContainText() -- Dit controleert of het 'Element' tekst bevat
- await expect(page.locator('.status')).toHaveText('Submitted'); 

Bij default staat de timeout for assertions ingesteld op 5 seconden. 
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

 
 

