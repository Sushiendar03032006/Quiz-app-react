# Quiz-app-react

# Aim: 
```
To create a quiz app in react js
```

# Procedure:
```
Create a folder and create a react app using this command npm create vite@latest quiz-app

Create a React component called QuizApp.

Use useReducer to manage the to manage quiz state (NEXT_QUESTION, ANSWER_SELECTED, RESET).

Use useRef to handle the timer interval ID (start, stop without re-renders)

Use useMemo to calculate the final score only when the quiz is finished (avoid recalculating every render)

Use useCallback to memoize functions like handleAnswerSelect and handleResetQuiz

Style the app to make beautiful by using css externally named quiz.css

Insert the quiz app in App.jsx for rendering.
```

# Program:
## quiz.jsx
```

import {
  useState,
  useReducer,
  useRef,
  useCallback,
  useEffect,
  useMemo,
} from "react";
import "./quiz.css";

const questions = [
  // EASY QUESTIONS
  { question: "Which language runs in a web browser?", options: ["Java", "C", "Python", "JavaScript"], answer: "JavaScript", difficulty: "easy" },
  { question: "What does CSS stand for?", options: ["Central Style Sheets", "Cascading Style Sheets", "Cascading Simple Sheets", "Cars SUVs Sailboats"], answer: "Cascading Style Sheets", difficulty: "easy" },
  { question: "Inside which HTML element do we put JavaScript?", options: ["<javascript>", "<script>", "<js>", "<code>"], answer: "<script>", difficulty: "easy" },
  { question: "Which CSS property changes text color?", options: ["text-color", "color", "font-color", "background-color"], answer: "color", difficulty: "easy" },
  { question: "Which HTML attribute specifies an alternate text for an image?", options: ["title", "alt", "src", "longdesc"], answer: "alt", difficulty: "easy" },
  { question: "How do you insert a comment in HTML?", options: ["// comment", "# comment", "<!-- This is a comment -->", "/* comment */"], answer: "<!-- This is a comment -->", difficulty: "easy" },
  { question: "Which tag is used to display a numbered list in HTML?", options: ["<ul>", "<ol>", "<li>", "<dl>"], answer: "<ol>", difficulty: "easy" },
  { question: "How do you declare a JavaScript variable?", options: ["var carName;", "variable carName;", "v carName;", "String carName;"], answer: "var carName;", difficulty: "easy" },
  { question: "Which event occurs when the user clicks on an HTML element?", options: ["onmouseover", "onchange", "onclick", "onmouseclick"], answer: "onclick", difficulty: "easy" },
  { question: "Which keyword is used to create a constant in JavaScript?", options: ["let", "constant", "var", "const"], answer: "const", difficulty: "easy" },

  // MEDIUM QUESTIONS
  { question: "What year was JavaScript launched?", options: ["1996", "1995", "1994", "None of the above"], answer: "1995", difficulty: "medium" },
  { question: "React is mainly used for building what?", options: ["Database", "User Interface", "Web Servers", "Operating Systems"], answer: "User Interface", difficulty: "medium" },
  { question: "Which method finds an HTML element by ID?", options: ["getElementById()", "getElementsByClass()", "querySelectorAll()", "getByTagName()"], answer: "getElementById()", difficulty: "medium" },
  { question: "Which CSS property controls text size?", options: ["font-style", "text-size", "font-size", "text-style"], answer: "font-size", difficulty: "medium" },
  { question: "How do you write 'Hello World' in an alert box in JavaScript?", options: ["msg('Hello World');", "alert('Hello World');", "prompt('Hello World');", "console.log('Hello World');"], answer: "alert('Hello World');", difficulty: "medium" },
  { question: "Which HTML tag is used to define an internal style sheet?", options: ["<style>", "<css>", "<script>", "<design>"], answer: "<style>", difficulty: "medium" },
  { question: "Which operator is used for strict equality in JavaScript?", options: ["==", "===", "=", "!="], answer: "===", difficulty: "medium" },
  { question: "Which property is used to change the background color in CSS?", options: ["bgcolor", "background-color", "color", "bg-color"], answer: "background-color", difficulty: "medium" },
  { question: "Which HTML tag is used for a hyperlink?", options: ["<link>", "<a>", "<href>", "<hyperlink>"], answer: "<a>", difficulty: "medium" },
  { question: "Which JavaScript method converts a JSON string to an object?", options: ["JSON.stringify()", "JSON.parse()", "JSON.object()", "JSON.convert()"], answer: "JSON.parse()", difficulty: "medium" },

  // HARD QUESTIONS
  { question: "Which of these is a JavaScript framework?", options: ["React", "Django", "Laravel", "Spring"], answer: "React", difficulty: "hard" },
  { question: "Which CSS unit is invalid?", options: ["px", "em", "cm", "ptt"], answer: "ptt", difficulty: "hard" },
  { question: "Which HTML tag is used for table header?", options: ["<th>", "<td>", "<thead>", "<header>"], answer: "<th>", difficulty: "hard" },
  { question: "Which method adds a new element at the end of an array in JavaScript?", options: ["push()", "pop()", "shift()", "unshift()"], answer: "push()", difficulty: "hard" },
  { question: "Which property is used to hide an element in CSS without removing its space?", options: ["display: none", "visibility: hidden", "opacity: 0", "hidden: true"], answer: "visibility: hidden", difficulty: "hard" },
  { question: "Which HTML5 element is used to embed video?", options: ["<video>", "<media>", "<movie>", "<embed>"], answer: "<video>", difficulty: "hard" },
  { question: "Which function is used to delay execution in JavaScript?", options: ["setTimeout()", "delay()", "pause()", "sleep()"], answer: "setTimeout()", difficulty: "hard" },
  { question: "Which CSS pseudo-class applies when the mouse is over an element?", options: [":hover", ":active", ":focus", ":mouse"], answer: ":hover", difficulty: "hard" },
  { question: "Which JavaScript array method removes the first element?", options: ["shift()", "pop()", "splice()", "slice()"], answer: "shift()", difficulty: "hard" },
  { question: "Which HTML attribute is used for inline styles?", options: ["class", "style", "id", "css"], answer: "style", difficulty: "hard" },

  // ADDITIONAL QUESTIONS TO MAKE ~30
  { question: "Which HTML tag defines a paragraph?", options: ["<p>", "<para>", "<paragraph>", "<text>"], answer: "<p>", difficulty: "easy" },
  { question: "Which operator is used for addition in JavaScript?", options: ["+", "-", "*", "/"], answer: "+", difficulty: "easy" },
  { question: "Which JavaScript keyword defines a function?", options: ["function", "def", "func", "method"], answer: "function", difficulty: "medium" },
  { question: "Which CSS property is used for flex container?", options: ["display: flex", "flex-container", "flex", "display: container"], answer: "display: flex", difficulty: "medium" },
  { question: "Which HTML tag is used to display an image?", options: ["<image>", "<img>", "<src>", "<picture>"], answer: "<img>", difficulty: "easy" },
  { question: "Which JavaScript loop iterates a fixed number of times?", options: ["for", "while", "do-while", "for-in"], answer: "for", difficulty: "medium" },
];


const initialState = {
  currentQuestion: null,
  score: 0,
  quizOver: false,
  selectedAnswer: null,
  difficulty: "easy", // start with easy question
  askedQuestions: [],
};

function reducer(state, action) {
  switch (action.type) {
    case "ANSWER_SELECTED":
      const correct = action.payload === state.currentQuestion.answer ? 1 : 0;

      // Determine next difficulty
      let nextDifficulty = state.difficulty;
      if (correct && state.difficulty === "easy") nextDifficulty = "medium";
      else if (correct && state.difficulty === "medium") nextDifficulty = "hard";
      else if (!correct && state.difficulty === "hard") nextDifficulty = "medium";
      else if (!correct && state.difficulty === "medium") nextDifficulty = "easy";

      return {
        ...state,
        score: state.score + correct,
        selectedAnswer: action.payload,
        nextDifficulty,
      };

    case "NEXT_QUESTION":
      // Filter remaining questions of chosen difficulty
      const remaining = questions.filter(
        (q) => !state.askedQuestions.includes(q.question) && q.difficulty === state.nextDifficulty
      );

      if (remaining.length === 0) {
        // If no more questions of desired difficulty, pick any remaining
        const allRemaining = questions.filter(
          (q) => !state.askedQuestions.includes(q.question)
        );
        if (allRemaining.length === 0) return { ...state, quizOver: true };
        return {
          ...state,
          currentQuestion: allRemaining[0],
          askedQuestions: [...state.askedQuestions, allRemaining[0].question],
          selectedAnswer: null,
          difficulty: state.nextDifficulty,
        };
      }

      return {
        ...state,
        currentQuestion: remaining[0],
        askedQuestions: [...state.askedQuestions, remaining[0].question],
        selectedAnswer: null,
        difficulty: state.nextDifficulty,
      };

    case "RESET":
      return { ...initialState, currentQuestion: questions[0], askedQuestions: [questions[0].question] };

    default:
      return state;
  }
}

export default function AdaptiveQuiz() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const timerRef = useRef(null);
  const [seconds, setSeconds] = useState(0);

  // Initialize first question
  useEffect(() => {
    dispatch({ type: "RESET" });
  }, []);

  useEffect(() => {
    timerRef.current = setInterval(() => setSeconds((s) => s + 1), 1000);
    return () => clearInterval(timerRef.current);
  }, []);

  useEffect(() => {
    if (state.quizOver) clearInterval(timerRef.current);
  }, [state.quizOver]);

  const finalPercentage = useMemo(() => {
    return state.quizOver ? Math.round((state.score / questions.length) * 100) : 0;
  }, [state.quizOver, state.score]);

  const handleAnswerSelect = useCallback(
    (option) => {
      if (!state.selectedAnswer) dispatch({ type: "ANSWER_SELECTED", payload: option });
    },
    [state.selectedAnswer]
  );

  const handleNext = useCallback(() => {
    dispatch({ type: "NEXT_QUESTION" });
  }, []);

  const handleResetQuiz = useCallback(() => {
    dispatch({ type: "RESET" });
    setSeconds(0);
    clearInterval(timerRef.current);
    timerRef.current = setInterval(() => setSeconds((s) => s + 1), 1000);
  }, []);

  return (
    <div className="quiz-container">
      <div className="quiz-box">
        <h1 className="quiz-title">Adaptive Quiz App</h1>
        <p className="timer">⏱ Time: {seconds}s</p>

        {!state.quizOver && state.currentQuestion ? (
          <>
            <p className={`difficulty ${state.difficulty}`}>Difficulty: {state.difficulty.toUpperCase()}</p>
            <h2 className="question-number">
              Question {state.askedQuestions.length} of {questions.length}
            </h2>
            <h2 className="question">{state.currentQuestion.question}</h2>
            <div className="options">
              {state.currentQuestion.options.map((option) => (
                <button
                  key={option}
                  onClick={() => handleAnswerSelect(option)}
                  className={`option-btn ${
                    state.selectedAnswer
                      ? option === state.currentQuestion.answer
                        ? "correct"
                        : option === state.selectedAnswer
                        ? "wrong"
                        : "disabled"
                      : ""
                  }`}
                  disabled={!!state.selectedAnswer}
                >
                  {option}
                </button>
              ))}
            </div>

            <div style={{ display: "flex", gap: "8px", justifyContent: "center" }}>
              <button className="reset-btn" onClick={handleResetQuiz}>
                Reset Quiz
              </button>
              {state.selectedAnswer && (
                <button className="next-btn" onClick={handleNext}>
                  {state.askedQuestions.length === questions.length ? "Finish" : "Next"}
                </button>
              )}
            </div>
          </>
        ) : (
          <div className="result">
            <h2>🎉 Quiz Over!</h2>
            <p className="final-score">Final Score: {state.score} / {questions.length}</p>
            <p>Time Taken: {seconds}s</p>

            <div className="progress-bar">
              <div className="progress-correct" style={{ width: `${(state.score / questions.length) * 100}%` }}></div>
              <div className="progress-wrong" style={{ width: `${((questions.length - state.score) / questions.length) * 100}%` }}></div>
            </div>
            <p className="progress-text">{finalPercentage}% Correct</p>
            <button className="reset-btn" onClick={handleResetQuiz}>Reset Quiz</button>
          </div>
        )}
      </div>
    </div>
  );
}
```

## quiz.css
```
/* ===== GENERAL STYLING ===== */
body {
  margin: 0;
  font-family: "Poppins", sans-serif;
  background: linear-gradient(135deg, #b3d1ff, #e5ccff);
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.quiz-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding: 15px;
}

/* ===== QUIZ BOX ===== */
.quiz-box {
  background-color: #fff;
  padding: 30px 25px;
  border-radius: 20px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.2);
  width: 100%;
  max-width: 450px;
  text-align: center;
  transition: 0.3s ease;
}

.quiz-title {
  font-size: 28px;
  color: #4b34a4;
  margin-bottom: 10px;
}

.timer {
  color: #555;
  font-size: 16px;
  margin-bottom: 15px;
}

/* ===== QUESTION ===== */
.question {
  font-size: 20px;
  font-weight: 600;
  color: #333;
  margin-bottom: 20px;
  line-height: 1.4;
}

/* ===== OPTIONS ===== */
.options {
  display: grid;
  gap: 12px;
  margin-bottom: 25px;
}

.option-btn {
  padding: 10px 14px;
  font-size: 16px;
  border-radius: 10px;
  border: 2px solid #ddd;
  background-color: #f9f9f9;
  cursor: pointer;
  transition: 0.25s ease;
  word-wrap: break-word;
}

.option-btn:hover {
  background-color: #ede9ff;
}

.option-btn.correct {
  background-color: #4caf50;
  color: #fff;
  border-color: #4caf50;
}

.option-btn.wrong {
  background-color: #f44336;
  color: #fff;
  border-color: #f44336;
}

.option-btn.disabled {
  opacity: 0.7;
  pointer-events: none;
}

/* ===== BUTTONS ===== */
.next-btn,
.reset-btn {
  background-color: #4b34a4;
  color: white;
  border: none;
  padding: 10px 18px;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  transition: 0.25s ease;
}

.next-btn:hover,
.reset-btn:hover {
  background-color: #382885;
}

/* ===== RESULT ===== */


.result h2 {
  color: #4caf50;
  font-size: 24px;
  margin-bottom: 8px;
}

.final-score {
  font-size: 18px;
  font-weight: 600;
  margin: 10px 0;
  color: #333;
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  .quiz-box {
    padding: 25px 20px;
    max-width: 90%;
  }

  .quiz-title {
    font-size: 24px;
  }

  .question {
    font-size: 18px;
  }

  .option-btn {
    font-size: 15px;
    padding: 8px 12px;
  }

  .next-btn,
  .reset-btn {
    font-size: 15px;
    padding: 8px 14px;
  }
}

@media (max-width: 480px) {
  body {
    background: linear-gradient(135deg, #d2e2ff, #f2e0ff);
  }

  .quiz-box {
    padding: 20px 15px;
    border-radius: 15px;
  }

  .quiz-title {
    font-size: 22px;
  }

  .question {
    font-size: 16px;
  }

  .option-btn {
    font-size: 14px;
  }

  .next-btn,
  .reset-btn {
    font-size: 14px;
  }
}

.question-number {
  font-size: 18px;
  font-weight: 600;
  color: #4b34a4;
  margin-bottom: 5px;
}

/* Progress Bar Styling */
.progress-bar {
  width: 100%;
  height: 25px;
  display: flex;
  border-radius: 10px;
  overflow: hidden;
  margin: 15px 0;
  border: 2px solid #ccc;
}

.progress-correct {
  background-color: #4caf50;
  height: 100%;
  transition: width 1s ease;
}

.progress-wrong {
  background-color: #f44336;
  height: 100%;
  transition: width 1s ease;
}

.progress-text {
  font-size: 16px;
  font-weight: 600;
  color: #333;
  margin-bottom: 15px;
  text-align: center;
}


/* difficulty indicator */


.difficulty {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 10px;
}

.difficulty.easy {
  color: #4caf50;
}

.difficulty.medium {
  color: #ff9800;
}

.difficulty.hard {
  color: #f44336;
}

```

## App.jsx
```

import './App.css'
import QuizApp from './quiz'

function App() {


  return (
    
      <div>
        <QuizApp/>

      </div>
    
  )
}

export default App

```

# Output
<img width="1908" height="933" alt="image" src="https://github.com/user-attachments/assets/cc99235c-26e5-451c-8527-74d6122b693d" />

# Result:
 The quiz app in react is create where users can answer multiple-choice questions,app tracks score,users can reset the quiz and the timer counts how long the quiz took is successfull added and created using react js.




