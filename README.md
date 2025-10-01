# React Quiz App

A small, focused React app that presents timed multiple‑choice questions, provides immediate visual feedback for selected answers, and displays a summary at the end. Built with React and Vite.

## Overview

- Timed questions with a visual progress bar per question
- Answer selection with staged UI states: selected → correct/wrong
- Automatic advance on selection or when time runs out (skip)
- Final summary with percentages for skipped, correct, and wrong answers

## Getting Started

- Prerequisites: Node.js 18+ and npm
- Install: `npm install`
- Development: `npm run dev`
- Build: `npm run build`
- Preview build: `npm run preview`

## Project Structure

- `src/App.jsx`: App shell that renders `Header` and `Quiz`.
- `src/components/Header.jsx`: Logo + app title.
- `src/components/Quiz.jsx`: Orchestrates quiz flow and overall state (`userAnswers`).
- `src/components/Question.jsx`: Manages per‑question UI state and timing transitions.
- `src/components/QuestionTimer.jsx`: Progress bar with countdown, timeouts, and cleanup.
- `src/components/Answers.jsx`: Stable shuffled answers and conditional button styles.
- `src/components/Summary.jsx`: Computes and displays final stats and a per‑question list.
- `src/questions.js`: Static question set (correct answer is always at index 0).

## How It Works

- `Quiz` stores `userAnswers` and derives `activeQuestionIndex` from its length. When answers length equals total questions, `Summary` renders.
- `Question` tracks local `answer` state `{ selectedAnswer, isCorrect }`. It stages timing:
  - On selection: show as “answered” for 1s.
  - Then evaluate correctness and show “correct”/“wrong” for 2s.
  - Then notify parent via `onSelectAnswer` to advance.
- `QuestionTimer` receives a `timeout` that changes with UI state. It uses `setTimeout` for the overall timeout and `setInterval` for smooth progress updates, with proper cleanup.
- `Answers` uses a `useRef` to compute and persist a shuffled copy of answers, so order stays stable across renders of a question.

## Important Concepts Learned

- Component State: Using `useState` for both global quiz flow (`userAnswers` in `Quiz`) and local per‑question UI (`answer` in `Question`).
- Derived State: Computing values like `activeQuestionIndex` and `quizIsComplete` from existing state instead of duplicating data.
- Immutability: Updating arrays with the spread operator (`setUserAnswers([...prev, value])`) to ensure React can detect state changes.
- Memoized Callbacks: Using `useCallback` in `Quiz` for `handleSelectAnswer` and `handleSkipAnswer` to keep function references stable when passed to children and effects.
- Component Remounting via Keys:
  - `Quiz` renders `<Question key={activeQuestionIndex} ... />` so moving to a new index resets the child’s local state and effects.
  - `Question` renders `<QuestionTimer key={timer} ... />` with a changing `key` so the timer restarts for each UI phase (select → evaluate → finalize).
- Side Effects and Cleanup: `QuestionTimer` uses `useEffect` to set up `setTimeout` and `setInterval`, and cleans both up on unmount or dependency changes to avoid memory leaks and stale updates.
- Conditional Rendering and UI States: Computing an `answerState` string ("", "answered", "correct", "wrong") that drives conditional classes and behavior.
- Preventing Re‑Shuffle: Persisting shuffled answers in a `useRef` so answers don’t reshuffle on every render of the same question.
- Controlled Time‑Driven UI: Coordinating multiple `setTimeout`s to stage UI feedback in a clear, user‑friendly sequence.
- List Rendering with Keys: Mapping answers and using a suitable `key` for predictable reconciliation.
- Asset Imports: Importing images (logo, complete badge) and using them in components—handled seamlessly by Vite.

## Notable Implementation Details

- Skip Behavior: If time expires and no answer is selected, `onTimeout` triggers the parent’s skip handler (records `null`). Once an answer is picked, `onTimeout` is disabled.
- Correctness Rule: The correct answer is the first item in each question’s `answers` array (`answers[0]`). The UI shuffles the displayed order, but correctness checks always reference index `0`.
- Progress Bar: Implemented with a native `<progress>` element, updated every 100ms for smooth visuals.

## Tech Stack

- React 19 (functional components + hooks)
- Vite for dev server and build
- ESLint with React/React Hooks plugins

## Scripts

- `npm run dev`: Start dev server
- `npm run build`: Production build
- `npm run preview`: Preview the production build

---

This project solidifies core React skills: component composition, local vs lifted state, effects with cleanup, memoization, derived state, conditional rendering, and identity via keys to control remounting and side‑effect lifecycles.
