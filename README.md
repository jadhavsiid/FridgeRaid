# FridgeRaid - Step-by-Step Build Checklist

This is a beginner-friendly build plan for FridgeRaid, a small app where you type an ingredient, see meals you can cook with it, and click a meal to read the full recipe. You will use only HTML, CSS and JavaScript, and the data comes from TheMealDB, a free API that needs no key.

How to use this file: tick each box as you finish a step. The code examples are only starting points to show you how a step can begin. Type them yourself, rename things, and fill in the rest on your own, because that is where the practice happens. Code examples sit right below the group of steps they belong to.

Total time: about 4 to 6 hours.

---

## What the UI looks like

```
+--------------------------------------------------+
|                   FridgeRaid                     |
|        Raid your fridge, find your dinner        |
|                                                  |
|   [ type an ingredient...        ] [Find Meals]  |
|                [ Surprise Me ]                   |
|                                                  |
|          Found 25 meals with chicken            |
|                                                  |
|   +--------+   +--------+   +--------+           |
|   | image  |   | image  |   | image  |           |
|   |        |   |        |   |        |           |
|   | Name 1 |   | Name 2 |   | Name 3 |           |
|   +--------+   +--------+   +--------+           |
|   +--------+   +--------+   +--------+           |
|   | image  |   | image  |   | image  |           |
|   | Name 4 |   | Name 5 |   | Name 6 |           |
|   +--------+   +--------+   +--------+           |
+--------------------------------------------------+
```

When you click a meal card, a popup (modal) opens over the page:

```
+--------------------------------------------------+
| (dark overlay over the whole page)               |
|     +----------------------------------+         |
|     |                              [X] |         |
|     |  Teriyaki Chicken Casserole      |         |
|     |  Japanese | Chicken              |         |
|     |  [        big image        ]     |         |
|     |                                  |         |
|     |  Ingredients                     |         |
|     |   - 3/4 cup Soy Sauce            |         |
|     |   - 1/2 cup Water                |         |
|     |   - ...                          |         |
|     |                                  |         |
|     |  Instructions                    |         |
|     |  Preheat oven to 350...          |         |
|     +----------------------------------+         |
+--------------------------------------------------+
```

The page has four states you will build: empty (nothing searched yet), loading ("Searching the fridge..."), results (grid of cards), and error or no results (a friendly message).

---

## API cheat sheet (keep this open in a tab)

- Search by ingredient: `https://www.themealdb.com/api/json/v1/1/filter.php?i=chicken_breast`
  - Returns `{ meals: [ { strMeal, strMealThumb, idMeal }, ... ] }`
  - If nothing matches, it returns `{ meals: null }`
  - Ingredients with spaces use underscores, like `chicken_breast`
- Meal details by id: `https://www.themealdb.com/api/json/v1/1/lookup.php?i=52772`
  - Returns `{ meals: [ { ...one meal with all details } ] }` (an array with one item)
- Random meal: `https://www.themealdb.com/api/json/v1/1/random.php`
  - Same shape as the lookup response
- Ingredients inside a meal come as `strIngredient1` to `strIngredient20`, and quantities as `strMeasure1` to `strMeasure20`. Unused slots are empty strings or null.

---

## Phase 0 - Project setup

- [ ] 0.1 Create a new folder called `fridge-raid` somewhere easy to find, like your Desktop.
- [ ] 0.2 Open VS Code, choose File > Open Folder, and select `fridge-raid`.
- [ ] 0.3 In the VS Code explorer panel, click the New File icon and create `index.html`.
- [ ] 0.4 Create a second file called `style.css`.
- [ ] 0.5 Create a third file called `script.js`.
- [ ] 0.6 (Optional but recommended) Install the Live Server extension in VS Code, then right-click `index.html` and choose Open with Live Server. The page will refresh every time you save.
- [ ] 0.7 In `index.html`, type `!` and press Tab to generate the HTML boilerplate. Change the `<title>` to `FridgeRaid`.
- [ ] 0.8 Inside `<head>`, link your CSS file.
- [ ] 0.9 Just before the closing `</body>` tag, link your JS file.
- [ ] 0.10 In `script.js`, write a `console.log("FridgeRaid is alive")`, open the browser, press F12, go to the Console tab, and confirm you see the message. If you do, your three files are connected correctly.

Example for 0.8 to 0.10:

```html
<!-- inside <head> -->
<link rel="stylesheet" href="style.css" />

<!-- just before </body> -->
<script src="script.js"></script>
```

```js
// script.js
console.log("FridgeRaid is alive");
```

---

## Phase 1 - HTML structure (no styling yet)

Goal: build every element the app will ever need, even the hidden ones. JavaScript will fill in the content later.

- [ ] 1.1 Inside `<body>`, create a `<header>` element.
- [ ] 1.2 Inside the header, add an `<h1>` with the text `FridgeRaid` and a `<p>` with the tagline `Raid your fridge, find your dinner`.
- [ ] 1.3 After the header, create a `<main>` element. Everything else goes inside it.
- [ ] 1.4 Inside `main`, create a `<form>` with the id `search-form`. Using a form means pressing Enter will also trigger a search.
- [ ] 1.5 Inside the form, add an `<input>` with `type="text"`, `id="ingredient-input"`, a placeholder like `Try chicken, egg, paneer...`, and `autocomplete="off"`.
- [ ] 1.6 Inside the form, after the input, add a `<button>` with `type="submit"` and the text `Find Meals`.
- [ ] 1.7 After the form (still inside `main`), add a second `<button>` with `type="button"`, `id="surprise-btn"` and the text `Surprise Me`. Use `type="button"` so it does not submit the form.
- [ ] 1.8 Add an empty `<p>` with `id="status"`. This will show messages like "Loading..." and "No meals found".
- [ ] 1.9 Add an empty `<section>` with `id="results"`. The meal cards will be placed here by JavaScript.
- [ ] 1.10 Below `main`, create the modal: a `<div>` with `id="modal"` and classes `modal hidden`.
- [ ] 1.11 Inside the modal div, create a `<div class="modal-content">`.
- [ ] 1.12 Inside the modal content, add a close `<button id="modal-close">` with the text `X`, and an empty `<div id="modal-body">`.
- [ ] 1.13 Save and look at the page. It will look plain and ugly. That is expected.

Example for 1.4 to 1.9:

```html
<form id="search-form">
  <input type="text" id="ingredient-input" placeholder="Try chicken, egg, paneer..." autocomplete="off" />
  <button type="submit">Find Meals</button>
</form>
<button type="button" id="surprise-btn">Surprise Me</button>
<p id="status"></p>
<section id="results"></section>
```

Example for 1.10 to 1.12:

```html
<div id="modal" class="modal hidden">
  <div class="modal-content">
    <button id="modal-close">X</button>
    <div id="modal-body"></div>
  </div>
</div>
```

---

## Phase 2 - CSS styling

Goal: make it look good. Do this before JavaScript so you can see what you are building toward.

- [ ] 2.1 In `style.css`, add a reset at the top so browsers do not add their own spacing.
- [ ] 2.2 Style `body`: pick a font (like `system-ui, sans-serif`), a soft background colour, and a dark text colour.
- [ ] 2.3 Style `header`: centre the text and add padding at top and bottom.
- [ ] 2.4 Style `#search-form`: make it a flex container, add a gap between items, give it a `max-width` (around 500px), and centre it using `margin: 0 auto`.
- [ ] 2.5 Style the input so it grows to fill the space (`flex: 1`), then add padding, a border and a border radius.
- [ ] 2.6 Style all buttons: padding, background colour, white text, no border, border radius, and `cursor: pointer`. Add a `:hover` colour change.
- [ ] 2.7 Style `#surprise-btn` so it sits centred under the form (hint: `display: block` and `margin: 16px auto`).
- [ ] 2.8 Style `#status`: centre the text and add some margin.
- [ ] 2.9 Style `#results` as a CSS grid with responsive columns, then add a gap and some padding.
- [ ] 2.10 To style the cards before JavaScript exists, temporarily paste one fake card into `#results` in your HTML, with a class `meal-card`, an `<img>` and an `<h3>`. Remove it later.
- [ ] 2.11 Style `.meal-card`: white background, border radius, a light box shadow, `overflow: hidden`, and `cursor: pointer`.
- [ ] 2.12 Style `.meal-card img` to be `width: 100%` and block level.
- [ ] 2.13 Style `.meal-card h3`: padding and a smaller font size.
- [ ] 2.14 Add a hover effect on `.meal-card` using `transform: translateY(-4px)` and a `transition`.
- [ ] 2.15 Style `.modal` as a full-screen overlay: `position: fixed`, `inset: 0`, a semi-transparent dark background, and flex centring.
- [ ] 2.16 Add a `.hidden` class with `display: none`. This is how you will show and hide the modal later.
- [ ] 2.17 Style `.modal-content`: white background, padding, a `max-width` (around 600px), `max-height: 85vh`, `overflow-y: auto`, and a border radius.
- [ ] 2.18 Style `#modal-close` so it sits in the top right corner of the box.
- [ ] 2.19 Remove the temporary fake card from step 2.10.

Example for 2.1, 2.9 and 2.16:

```css
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

#results {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 20px;
  padding: 20px;
}

.hidden {
  display: none;
}
```

---

## Phase 3 - Connect JavaScript to the page

Goal: grab references to the elements you will use, and store them in variables.

- [ ] 3.1 Delete the test `console.log` from Phase 0 (or keep it until you are sure everything works).
- [ ] 3.2 Create a constant to store the base API URL so you do not retype it everywhere.
- [ ] 3.3 Create a variable for the search form using `document.querySelector`.
- [ ] 3.4 Create a variable for the ingredient input.
- [ ] 3.5 Create a variable for the surprise button.
- [ ] 3.6 Create a variable for the status paragraph.
- [ ] 3.7 Create a variable for the results section.
- [ ] 3.8 Create variables for the modal, the modal body, and the modal close button.
- [ ] 3.9 Log a couple of these variables to the console and check that none of them print `null`. If one is `null`, your id in JS does not match your id in HTML.

Example:

```js
const BASE_URL = "https://www.themealdb.com/api/json/v1/1";

const searchForm = document.querySelector("#search-form");
const ingredientInput = document.querySelector("#ingredient-input");
// ...continue for the other elements
```

---

## Phase 4 - Your first fetch

Goal: type an ingredient, press the button, and see data arrive in the console. No cards yet.

- [ ] 4.1 Add a `submit` event listener to the search form.
- [ ] 4.2 Inside the listener, call `event.preventDefault()` as the very first line. Without it, the browser reloads the page when the form is submitted.
- [ ] 4.3 Read the text from the input using `.value`, and store it in a variable.
- [ ] 4.4 Clean it up: remove spaces from both ends with `.trim()` and convert it to lowercase with `.toLowerCase()`.
- [ ] 4.5 If the cleaned text is empty, show a message in the status paragraph (like "Type an ingredient first") and use `return` to stop.
- [ ] 4.6 Convert spaces inside the text to underscores, since the API wants `chicken_breast` and not `chicken breast`.
- [ ] 4.7 Create a new function called `searchMeals` and mark it `async`. It should take the ingredient as a parameter.
- [ ] 4.8 Inside `searchMeals`, build the full URL using a template string with your base URL and the ingredient.
- [ ] 4.9 Call `fetch` with that URL and `await` the result. Store it in a variable called `response`.
- [ ] 4.10 Convert the response to JSON using `await response.json()`. Store it in a variable called `data`.
- [ ] 4.11 `console.log(data)` and call `searchMeals` from your submit listener.
- [ ] 4.12 Test with `chicken`, then `chicken breast`, then a nonsense word like `asdfgh`. Open the logged object in the console and look at the shape. For the nonsense word, check what `data.meals` is.

Example:

```js
searchForm.addEventListener("submit", (event) => {
  event.preventDefault();
  const ingredient = ingredientInput.value.trim().toLowerCase();
  // ...check if empty, replace spaces, then call searchMeals(...)
});

async function searchMeals(ingredient) {
  const url = `${BASE_URL}/filter.php?i=${ingredient}`;
  const response = await fetch(url);
  const data = await response.json();
  console.log(data);
}
```

---

## Phase 5 - Show the results as cards

Goal: turn the array of meals into visible cards on the page.

- [ ] 5.1 Create a function called `renderMeals` that takes the `meals` array as a parameter.
- [ ] 5.2 At the start of the function, clear the old results by setting the results section's `innerHTML` to an empty string. Otherwise every new search will stack under the last one.
- [ ] 5.3 Loop over the meals using `forEach`.
- [ ] 5.4 Inside the loop, create a new `div` using `document.createElement`.
- [ ] 5.5 Give that div the class `meal-card`.
- [ ] 5.6 Store the meal's id on the div using a data attribute (`card.dataset.id = meal.idMeal`). You will need this in Phase 7 to know which card was clicked.
- [ ] 5.7 Set the div's `innerHTML` to an `<img>` (using `meal.strMealThumb` and an alt text) and an `<h3>` (using `meal.strMeal`).
- [ ] 5.8 Append the div to the results section.
- [ ] 5.9 Back in `searchMeals`, handle the case where `data.meals` is `null`: show "No meals found, try another ingredient" in the status paragraph, clear the grid, and `return`.
- [ ] 5.10 Otherwise, call `renderMeals(data.meals)`.
- [ ] 5.11 Show a count in the status paragraph, like `Found 25 meals with chicken`.
- [ ] 5.12 Test: chicken, a nonsense word, then chicken again. The grid should reset every time.

Example:

```js
function renderMeals(meals) {
  resultsSection.innerHTML = "";
  meals.forEach((meal) => {
    const card = document.createElement("div");
    card.classList.add("meal-card");
    card.dataset.id = meal.idMeal;
    // ...set card.innerHTML with the image and name
    // ...append card to resultsSection
  });
}
```

---

## Phase 6 - Loading and error handling

Goal: the app should never look frozen or broken.

- [ ] 6.1 Create a small helper function `setStatus(message)` that puts text into the status paragraph. You will call it from many places.
- [ ] 6.2 Replace your direct status updates from earlier phases with calls to `setStatus`.
- [ ] 6.3 At the start of `searchMeals`, call `setStatus("Searching the fridge...")` and clear the results grid.
- [ ] 6.4 After the `fetch`, check `response.ok`. If it is false, throw an error with a message, because fetch does not throw on 404 or 500 by itself.
- [ ] 6.5 Wrap everything inside `searchMeals` in a `try { } catch (error) { }` block.
- [ ] 6.6 In the `catch` block, show a friendly message with `setStatus` and log the real error with `console.error(error)`.
- [ ] 6.7 Disable the Find Meals button while a search is running, and enable it again afterwards. Put the enabling code in a `finally { }` block so it always runs, even after an error.
- [ ] 6.8 Test the error path: in DevTools, open the Network tab, set throttling to Offline, and search. You should see your friendly message instead of a silent failure.

Example:

```js
async function searchMeals(ingredient) {
  setStatus("Searching the fridge...");
  try {
    const response = await fetch(`${BASE_URL}/filter.php?i=${ingredient}`);
    if (!response.ok) {
      throw new Error("Request failed with status " + response.status);
    }
    // ...rest of the logic
  } catch (error) {
    console.error(error);
    setStatus("Something went wrong. Check your internet and try again.");
  } finally {
    // re-enable the button here
  }
}
```

---

## Phase 7 - Detect which card was clicked (event delegation)

Goal: one click listener on the whole grid, not one per card.

- [ ] 7.1 Add a `click` event listener on the results section (the parent), not on the cards.
- [ ] 7.2 Inside the listener, `console.log(event.target)` and click on a card's image, its title, and the card edge. Notice that the target changes depending on where you click.
- [ ] 7.3 Use `event.target.closest(".meal-card")` to find the card no matter which child element was clicked. Store the result in a variable.
- [ ] 7.4 If `closest` returned `null` (you clicked empty space in the grid), `return` to stop.
- [ ] 7.5 Read the meal id from the card with `card.dataset.id`.
- [ ] 7.6 `console.log` the id and click several cards to verify that each one logs a different id.

Example:

```js
resultsSection.addEventListener("click", (event) => {
  const card = event.target.closest(".meal-card");
  if (!card) return;
  const mealId = card.dataset.id;
  console.log(mealId);
});
```

---

## Phase 8 - Fetch the full recipe (the second fetch)

Goal: use the id from Phase 7 to get the full details of one meal.

- [ ] 8.1 Create an `async` function called `getMealDetails` that takes an id.
- [ ] 8.2 Build the lookup URL using your base URL and the id.
- [ ] 8.3 Fetch it, check `response.ok`, and convert it to JSON, just like Phase 4 and 6. Wrap it in `try/catch`.
- [ ] 8.4 Remember that `data.meals` is an array with one item. Pull out the meal using index `0` and store it in a variable.
- [ ] 8.5 Return the meal from the function.
- [ ] 8.6 Call `getMealDetails(mealId)` from your click listener (you will need to use `await`, so make the listener callback `async`) and log the returned meal.
- [ ] 8.7 Look at the logged meal in the console. Find `strInstructions`, `strCategory`, `strArea`, and scroll down to see `strIngredient1`, `strIngredient2`, and so on.
- [ ] 8.8 Create a helper function `getIngredients(meal)` that returns an array of strings like `"3/4 cup Soy Sauce"`.
- [ ] 8.9 Inside it, create an empty array, then loop from `1` to `20` with a normal `for` loop.
- [ ] 8.10 In each loop turn, read `meal["strIngredient" + i]` and `meal["strMeasure" + i]`. You must use square brackets here because the property name is built from a variable.
- [ ] 8.11 Only push to the array if the ingredient is not empty (check for both `null` and an empty string, and use `.trim()`).
- [ ] 8.12 Return the array and log it for a few different meals to check it looks right.

Example for 8.8 to 8.12:

```js
function getIngredients(meal) {
  const list = [];
  for (let i = 1; i <= 20; i++) {
    const name = meal["strIngredient" + i];
    const amount = meal["strMeasure" + i];
    // ...if name exists and is not blank, push `${amount} ${name}` into list
  }
  return list;
}
```

---

## Phase 9 - Show the recipe in the modal

Goal: display the recipe details in the popup.

- [ ] 9.1 Create a function `renderModal(meal)` that takes the meal object.
- [ ] 9.2 Inside it, call `getIngredients(meal)` and store the array.
- [ ] 9.3 Turn the ingredients array into HTML list items using `.map()` and `.join("")`.
- [ ] 9.4 Set the `innerHTML` of the modal body to a block containing: the meal name in an `<h2>`, the area and category in a `<p>`, a large image, an "Ingredients" heading with a `<ul>`, and an "Instructions" heading with the instructions text in a `<p>`.
- [ ] 9.5 Create a function `openModal()` that removes the `hidden` class from the modal.
- [ ] 9.6 Call `renderModal(meal)` and then `openModal()` from your click listener after `getMealDetails` returns.
- [ ] 9.7 Test: click a card and check that the modal opens with the correct details.
- [ ] 9.8 Optional: the instructions come as one long paragraph. Try splitting on line breaks (`.split("\n")`) and wrapping each part in its own `<p>` for easier reading.

Example for 9.3 and 9.4:

```js
const ingredientItems = ingredients.map((item) => `<li>${item}</li>`).join("");

modalBody.innerHTML = `
  <h2>${meal.strMeal}</h2>
  <p>${meal.strArea} | ${meal.strCategory}</p>
  <ul>${ingredientItems}</ul>
  `;
// ...add the image and the instructions yourself
```

---

## Phase 10 - Close the modal (three ways)

Goal: let users close the popup in the ways they expect.

- [ ] 10.1 Create a function `closeModal()` that adds the `hidden` class back to the modal.
- [ ] 10.2 Add a click listener to the X button that calls `closeModal`.
- [ ] 10.3 Add a click listener to the modal overlay itself. Inside, check that `event.target === modal`, so clicking the white box does not close it, only the dark area around it does.
- [ ] 10.4 Add a `keydown` listener on `document`. Inside, check if `event.key === "Escape"` and call `closeModal`.
- [ ] 10.5 Test all three ways of closing, then open another meal to make sure the old content is replaced.

Example for 10.3 and 10.4:

```js
modal.addEventListener("click", (event) => {
  if (event.target === modal) closeModal();
});

document.addEventListener("keydown", (event) => {
  if (event.key === "Escape") closeModal();
});
```

---

## Phase 11 - Surprise Me button

Goal: one click gives a random meal.

- [ ] 11.1 Add a `click` listener to the surprise button.
- [ ] 11.2 Inside it, create an `async` function (or make the callback async) that fetches `random.php`.
- [ ] 11.3 Check `response.ok`, convert to JSON, and take `data.meals[0]`.
- [ ] 11.4 Reuse `renderModal` and `openModal` with that meal. You should not need to write any new display code.
- [ ] 11.5 Wrap everything in `try/catch` with a friendly error message.
- [ ] 11.6 Test it five or six times to see different meals.
- [ ] 11.7 Think about this: you now have three places that fetch and parse JSON. Is there something you could pull out into a shared helper function like `fetchJSON(url)`? Try refactoring it.

---

## Phase 12 - Testing and cleanup

- [ ] 12.1 Test an empty search (press the button with nothing typed).
- [ ] 12.2 Test a search with extra spaces, like `  Chicken  `.
- [ ] 12.3 Test a two-word ingredient, like `chicken breast` or `minced beef`.
- [ ] 12.4 Test a nonsense word.
- [ ] 12.5 Test with the internet turned off in DevTools (Network tab > Offline).
- [ ] 12.6 Click several different cards in a row quickly.
- [ ] 12.7 Resize the browser to a phone width and check the grid and the modal.
- [ ] 12.8 Remove leftover `console.log` calls.
- [ ] 12.9 Add short comments above each function explaining what it does in your own words.
- [ ] 12.10 Check that your function and variable names are consistent and readable.
- [ ] 12.11 (Optional) Put the project on GitHub and publish it free using GitHub Pages or Netlify, so you can share the link.

---

## Phase 13 - Stretch goals (only after everything above works)

- [ ] 13.1 Multi-ingredient search: let users type `chicken, rice`. Split on commas, fetch each ingredient with `Promise.all`, and keep only meals whose `idMeal` appears in every result.
- [ ] 13.2 Debounced live search: search automatically 500ms after the user stops typing, using `setTimeout` and `clearTimeout`.
- [ ] 13.3 Cancel outdated requests with `AbortController`, so a slow old search can never overwrite a newer one.
- [ ] 13.4 Favourites: add a heart button in the modal and save favourite meal ids in `localStorage`.
- [ ] 13.5 Missing ingredients: let the user type what they have, and in the modal highlight which recipe ingredients they do not have.
- [ ] 13.6 Add a link to the recipe's YouTube video (`meal.strYoutube`) in the modal.

---

## Things to understand before an interview

When the project is finished, make sure you can explain these in your own words:

- [ ] Why `event.preventDefault()` is needed on a form submit
- [ ] What `async` and `await` do, and what a Promise is
- [ ] Why `fetch` does not throw on a 404, and why you check `response.ok`
- [ ] What `try / catch / finally` are each for
- [ ] What event delegation is, and why it beats one listener per card
- [ ] The difference between `event.target` and `event.currentTarget`
- [ ] Why bracket notation was needed in `getIngredients`
- [ ] What `.map()` and `.join()` did in the modal
