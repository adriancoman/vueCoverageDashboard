# Coverage dashboard

Small over-the-weekend project that I created in order to display a history of code-coverage and unit tests.

<img width="647" alt="Screenshot 2023-04-17 at 15 38 41" src="https://user-images.githubusercontent.com/4348190/232489884-bd9e2d6b-7283-4f52-8d3b-79921778e437.png">

## Tech stack used
### Tech in this repo:
- **Vue**: UI framework
- **Supabase**: API & DB storage
- **Chart.js**: For displaying historic data 
### Tech not covered in this repo:
- **Fastlane**: For running the CI/CD pipeline that checks the number of tests and code coverage and uploads the data to Supabase
- **Jenkins**: For running fastlane

## Why?
Because I wanted to have a place where me and other people from the team can track the code coverage and number of unit tests of our projects.
Of course, there already are other projects that do the same thing (CodeCov), but I decided to build my own solution because:
- I wanted to experiment with a new technology that I'm not familiar with and learn something new
- I needed only a fraction of what other code coverage platforms were using

## Installation
1. Install node.js
2. Install Vue.js
3. Add an .env file in the root of the project that looks like this:
```
VITE_SUPABASE_URL="{your_url}"
VITE_SUPABASE_ANON_KEY="{your_key}"
```
4. Run `npm install`
5. Run `npm run dev`

## DB Structure
The DB will return the following type of that:
```
  [{ created_at: '2023-04-16T13:50:45.632605+00:00', code_coverage: 4.5, test_count: 100, author: 'Bursucel', pull_request: `pull/123` },
  { created_at: '2023-04-16T13:50:45.632605+00:00', code_coverage: 12, test_count: 100, author: 'Ricky', pull_request: `pull/231` },
  { created_at: '2023-04-17T13:50:45.632605+00:00', code_coverage: 13.2, test_count: 103, author: 'Sushi', pull_request: `pull/241` }]
```

<br> 
_Disclaimer if you can't tell from the code:_ I have 0 experience in Vue. I'm an Android developer, with experience in Kotlin :)
