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
Playwright Inspector - https://playwright.dev/docs/debug#playwright-inspector is een GUI tool dat helpt om test script te debuggen en te schrijven.

Er is ook een mogelijkheid om een recording te maken -https://playwright.dev/docs/codegen-intro#running-codegen 
Door de kliken de record knop kunnen je activiteiten opgenomen worden en zal Playwright code genereren in de taal die we geselecteerd hebben.
#### 5.5. Gebruik van test hooks
Je kan gebruik maken van verschillende zoals zij het noemen, "test hooks". 
B.v. een test.afterAll  wordt uitgevoerd nadat alle andere test gedraaid hebben:
test.afterAll(async () => {
  console.log('Done with tests');
  // ...
});
En zo zijn er meer zoals test.beforeAll, test.beforeEach, test.afterEach en meer om mee te spelen

https://playwright.dev/docs/api/class-test#test-before-all

#### 5.6. Reports
Playwright komt met een paar built-in reporters - https://playwright.dev/docs/test-reporters 

#### 5.7. Hoe run je Playwright Tests sequenteel in dezelfde browser
Elke keer als Playwright een test uitvoert dan doet hij dit in een nieuwe Browser. Dus als je in de eerste test een login uitvoert dan gaat hij niet in deze Browser met die resultaten verder in het test proces. 
Het voordeel kan zijn dat je zelfstandige tests kan schrijven die je paralell kan uitvoeren met minder executie tijd.

Een oplossing om ervoor te zorgen dat de tests in dezelfde browser gedraaid worden zou volgens deze site - https://dzone.com/articles/execute-playwright-test-sequentially-same-browser-context op de volgende manier gedaan kunnen worden.

##### 1. Define the Page Instance in `beforeAll()`
```//inside your describe block  
let page:Page; //create variable with page
  test.beforeAll(async ({browser}) =>{
    page = await browser.newPage(); //Create a new Page instance
  }); 
 ```
  
##### 2. Do Not Pass the Page as a Parameter to Your Tests
```//inside describe block, after beforeAll()  
test('Navigate to Google', async () => {
    await page.goto('https://google.com/');
    const url=await page.url();
    expect(url).toContain('google');
  });
  
  test('Search for Playwright', async () => {
    await page.type('input[name="q"]',"Playwright")
    await page.keyboard.press('Enter');
    let text=await page.innerText('//h3[contains(text(),"Playwright:")]')
    expect(text).toContain('Playwright: Fast and reliable');
  });
```
##### 3. Alles samenvoegend
```import { test, expect, Page } from '@playwright/test';

test.describe('test', async () => {
  let page: Page;
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
 ```
 

