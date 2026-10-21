# Orbital quiz

## Run and validate

- Open `index.html` directly in a browser, preferably the integrated browser. From PowerShell: `Start-Process .\index.html`.
- Preserve direct `file://` execution: HTML, CSS, JavaScript, and SVG assets are embedded in `index.html`. Keep the application free of servers, dependencies, remote fonts, and external asset requests.
- Validate changes in the actual browser page. For one focused scoring check, reload, choose **Mars** on question 1, and verify `1 point`, green feedback, disabled answers, an enabled Next button, and progress value `1`.
- For the incorrect path, choose **Venus** on question 1: score remains zero, the selection shakes red, Mars is revealed in green, and the explanation names the correct answer.
- For a full run, the zero-based correct answer indices are `[1, 2, 3, 0, 1, 2, 3, 0, 1, 2]`. Check mixed answers, result reactions at scores 0, 4, 7, and 10, and replay resetting score, progress, history, and question controls.
- For missed-answer review, complete a mixed run and expand/collapse the review. Check only missed questions appear in original order with the actual selection, correct answer, and fact; disclosure must not change score/history/progress. Check a perfect run shows the all-correct message and replay clears review content and expansion state.
- Exercise native Tab/Shift+Tab and Enter/Space separately from A-D shortcuts. Synthetic `KeyboardEvent` dispatch tests shortcut handlers, not native focus navigation or button activation.
- Browser automation may block local `file://` navigation even when the integrated browser can open the file. Do not report native keyboard checks as passed when only DOM focus or synthetic events were tested.

## Architecture and state flow

- `index.html` contains theme/layout CSS, semantic page markup with an inline SVG scene, and a classic script at the end of the body. There is no module loader; the script expects its DOM targets to exist.
- The intro is independent of quiz scoring. It contains the mission-control orbital display, animation toggle, and static mission briefing.
- `questions` is the content source: each entry has `category`, `question`, four `options`, a zero-based `answer`, and an explanatory `fact`. Keep the ten-question contract; introductory copy and result thresholds also assume ten.
- Quiz state is held in `current`, `score`, `answered`, and `history`, an array of `{ selected, correct }` entries in question order. `selected` is the chosen option's zero-based index. State lasts only for the current page load.
- `renderQuestion(focus)` rebuilds answer buttons, clears feedback, and gates Next. Both answer clicks and case-insensitive A-D shortcuts call `selectAnswer(index)`.
- `selectAnswer` permits one submission per question, records correctness, updates score and progress, disables all choices, reveals feedback, and focuses Next. Progress counts answered questions, including incorrect ones.
- Next advances explicitly; there is no automatic advance or timer. The last question switches the action to See results.
- `showResults` swaps the question body for the result view, derives the reaction from score, and renders one history marker per answer. The restart handler resets quiz state and calls `renderQuestion(true)`.
- `renderReview` builds a read-only list from incorrect history entries, including selected/correct answers and the existing fact. Its native button toggles `aria-expanded` and the list's `hidden` state without changing quiz state. Perfect scores show an all-correct message instead; replay clears the list and disclosure state.

## UI and accessibility conventions

- Use the existing `:root` semantic color variables and their `prefers-color-scheme: dark` overrides. Keep the narrow centered layout, system sans-serif stack, 15px body text, and mobile adjustments at 480px.
- Correct and incorrect states combine color, text, and symbols: `.answer.correct`/`.answer.wrong`, `.feedback.good`/`.feedback.bad`, and `.mission-dot.good`/`.mission-dot.bad`.
- Keep `#progress`'s `aria-valuenow` synchronized with the visual `scaleX` fill. Score and feedback use polite live announcements.
- Preserve native buttons, visible `:focus-visible` styling, the answers group's question label, and programmatic focus targets (`tabindex="-1"` on question/result headings). After an answer focus goes to Next; after advancing or restarting it goes to the question; results focus their heading.
- Keyboard shortcuts ignore Alt/Ctrl/Meta combinations, repeats, already-answered questions, and the results screen.
- The motion toggle uses a stable label plus `aria-pressed`, and toggles `.intro.motion-paused` to pause SVG animations. `prefers-reduced-motion: reduce` disables animations/transitions and hides this toggle.
- Keep the orbital artwork inline and preserve its accessible SVG title. The satellite is rendered twice through `#orbit-back` and `#orbit-front` clipping regions; both copies must share the same path and animation timing to appear as one satellite passing behind and in front of the planet.
- The Sardinian flag uses four references to `#moor`. Preserve all four figures, the red cross, and white field.
- DOM IDs are the contract between markup and the `$` lookup helper. Render question/answer/fact copy with `textContent` and DOM creation; the existing `innerHTML` uses are limited to application-generated numeric status markup.
