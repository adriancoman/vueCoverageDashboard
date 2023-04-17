# Coverage dashboard

Small over-the-weekend project that I created in order to display a history of code-coverage and unit tests.


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

<br> 
_Disclaimer if you can't tell from the code:_ I have 0 experience in Vue. I'm an Android developer, with experience in Kotlin :)
