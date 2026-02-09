# public/index.html 🖼️
This HTML template boots the React app in the browser. It defines meta tags, links the favicon, and provides the root `<div>` where React mounts its components.

## Structure
- `<!DOCTYPE html>` declares HTML5.
- `<head>` contains metadata and links.
- `<body>` hosts `<div id="root">` and a `<noscript>` message.

## Key Elements
- **Meta tags** for charset, viewport, theme color, and description.
- **Favicon link** points to a bus-station icon.
- **`%PUBLIC_URL%` comments** explain asset path handling by Create React App.
- **`<div id="root">`** is the React mount point.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link
      rel="icon"
      href="https://icon-library.com/images/bus-station-icon/bus-station-icon-17.jpg"
    />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <title>Transport Management</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
```

## Relationships
- Loaded directly by the development server or production build.
- Provides `#root` for **src/index.js** to render the React tree.
- References **public/manifest.json** and **public/robots.txt** for PWA and crawler rules.

---

# public/manifest.json 📱
Defines Progressive Web App metadata, enabling installation on devices.

## Purpose
- Supplies icons, display mode, and theme colors.
- Guides browsers when the web app is “installed” to home screens.

## Icons Table

| src         | sizes                  | type           |
|-------------|------------------------|----------------|
| `favicon.ico`  | `64x64 32x32 24x24 16x16` | `image/x-icon` |
| `logo192.png` | `192x192`             | `image/png`    |
| `logo512.png` | `512x512`             | `image/png`    |

```json
{
  "short_name": "React App",
  "name": "Create React App Sample",
  "icons": [
    { "src": "favicon.ico",  "sizes": "64x64 32x32 24x24 16x16", "type": "image/x-icon" },
    { "src": "logo192.png", "type": "image/png", "sizes": "192x192" },
    { "src": "logo512.png", "type": "image/png", "sizes": "512x512" }
  ],
  "start_url": ".",
  "display": "standalone",
  "theme_color": "#000000",
  "background_color": "#ffffff"
}
```

## Relationships
- Referenced by **public/index.html** for PWA setup.
- Used by service workers in production builds (Create React App default).

---

# public/robots.txt 🤖
Controls how web crawlers index the site.

```txt
# https://www.robotstxt.org/robotstxt.html
User-agent: *
Disallow:
```

- **User-agent: \*** applies to all crawlers.
- **Disallow:** blank means full access is allowed.

## Relationships
- Served at `/robots.txt` in production.
- Helps SEO tools and search engines understand crawling rules.

---

# src/index.js
Entry point for the React application. It renders the root component into the DOM.

## Sequence
1. Imports React, ReactDOM, global CSS, `App`, and `reportWebVitals`.
2. Finds `<div id="root">` in **public/index.html**.
3. Renders `<App />` inside `<React.StrictMode>`.
4. Calls `reportWebVitals()` to measure performance if needed.

```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// To start measuring performance:
// reportWebVitals(console.log);
reportWebVitals();
```

## Relationships
- Mounts the **App.js** component.
- Hooks into **public/index.html**.
- Integrates **reportWebVitals.js** for optional analytics.

---

# src/App.js 🔐
Core component handling authentication, registration, and role-based routing.

## Overview
- Manages state for login/register forms and JWT token.
- Supports three user journeys:
  1. **Admin** → renders `<AdminPage />`.
  2. **Student** → renders `<StudentDashboard />`.
  3. **Teacher** → renders `<TeacherDashboard />`.
- Performs simple email and enrollment validation.

## Key State & Handlers
- `isRegister` toggles between login and registration.
- `userType` selects student or teacher.
- `users` holds mock-registered user data.
- `isAdminAuthenticated` gates admin access.
- `handleRegister`, `handleLogin`, `handleAdminLogin`, `handleLogout` drive flow.

```js
import React, { useState } from "react";
import "./App.css";
import AdminPage from "./Adminpage.js";
import StudentDashboard from "./StudentDashboard.js";
import TeacherDashboard from "./TeacherDashboard.js";

function App() {
  // Authentication & registration state
  const [isRegister, setIsRegister] = useState(false);
  const [userType, setUserType] = useState("student");
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [enrollmentNumber, setEnrollmentNumber] = useState("");
  const [users, setUsers] = useState({});
  const [isAdminLogin, setIsAdminLogin] = useState(false);
  const [isAdminAuthenticated, setIsAdminAuthenticated] = useState(false);
  const [loggedInStudent, setLoggedInStudent] = useState(null);
  const [loggedInTeacher, setLoggedInTeacher] = useState(null);
  const [token, setToken] = useState(null);

  // ...
  // Registration and login logic omitted for brevity
  // ...

  if (isAdminAuthenticated) {
    return <AdminPage onLogout={handleLogout} token={token} />;
  }
  if (loggedInStudent) {
    return (
      <StudentDashboard
        student={loggedInStudent}
        onLogout={handleLogout}
        token={token}
      />
    );
  }
  if (loggedInTeacher) {
    return (
      <TeacherDashboard
        teacher={loggedInTeacher}
        onLogout={handleLogout}
        token={token}
      />
    );
  }
  // Renders the login/register form...
}

export default App;
```

## Relationships
- **Imported by** `src/index.js`.
- **Imports** `src/Adminpage.js`, `src/StudentDashboard.js`, and `src/TeacherDashboard.js`.
- CSS styling via `App.css`.

---

# src/Adminpage.js 🚦
Admin interface for parking management and student bus assignments.

## Responsibilities
- Generates dummy data: students, parking slots, passes.
- Renders a toggleable sidebar with **Parking**, **Bus Records**, and **Logout**.
- Supports:
  - **Allocate slot**: assigns a parking slot with owner, plate, pass.
  - **Override slot**: edit or clear existing slot.
  - **Bus & Student Records**: view by city and bus, edit student details, change bus assignment.

```js
import React, { useState } from 'react';
import './Adminpage.css';
import { FaSignOutAlt, FaCar, FaUsers, FaBars } from 'react-icons/fa';

// Utility generators: enrollment, names, passes, students, parking slots...
// Sidebar and AdminPage component omitted for brevity.

export default AdminPage;
```

## Features
- **Data generation** for a realistic demo.
- **Complex state** manages multiple overlays and forms.
- **Iconography** via `react-icons/fa`.

## Relationships
- **Imported by** `src/App.js`.
- **Styles** in `src/Adminpage.css`.

---

# src/StudentDashboard.js 🎓
Student-facing dashboard for profile and bus services.

## Responsibilities
- Splits logged-in student's email into first/last names.
- Holds sidebar state for sections:
  - **Student Details**
  - **Bus Details**
  - **Register for Bus**
  - **Request Change**
- Generates dummy bus routes and timings if none exist.
- Provides components:
  - `<StudentDetails />`
  - `<BusDetails />`
  - `<BusRegistration />`
  - `<BusChangeRequest />`

```js
import React, { useState } from 'react';
import './StudentDashboard.css';

// SidebarItem, Sidebar, content components...
// Main StudentDashboard omitted.

export default StudentDashboard;
```

## Relationships
- **Imported by** `src/App.js`.
- CSS styling in `src/StudentDashboard.css`.
- Uses `react-icons` for menu toggles.

---

# src/TeacherDashboard.js 👩‍🏫
Teacher portal to register and manage vehicles.

## Responsibilities
- Parses teacher’s name from email.
- Sidebar sections:
  - **Teacher Details**
  - **Register Vehicle**
  - **Registered Vehicles**
  - **Logout**
- Lets teachers:
  - Register a new vehicle (limits one per type).
  - View, edit, or delete registered vehicles.
  - See allocated parking slots.

```js
import React, { useState } from 'react';
import './TeacherDashboard.css';
import {
  FaUserTie, FaSignOutAlt, FaCar, FaPlusCircle,
  FaTrashAlt, FaBars, FaEdit
} from 'react-icons/fa';

// SidebarItem, Sidebar, content components...
// Main TeacherDashboard omitted.

export default TeacherDashboard;
```

## Relationships
- **Imported by** `src/App.js`.
- Styles defined in `src/TeacherDashboard.css`.
- Leverages `react-icons` for UI.

---

# src/App.test.js 🧪
Basic test for the `App` component using React Testing Library.

```js
import { render, screen } from '@testing-library/react';
import App from './App';

test('renders learn react link', () => {
  render(<App />);
  const linkElement = screen.getByText(/learn react/i);
  expect(linkElement).toBeInTheDocument();
});
```

## Purpose
- Verifies that the “learn react” link (default CRA content) renders.
- Scaffold test demonstrating setup works.

## Relationships
- **Tests** `src/App.js`.
- Requires **src/setupTests.js** for custom matchers.

---

# src/reportWebVitals.js 📈
Utility to measure and report web performance metrics.

```js
const reportWebVitals = onPerfEntry => {
  if (onPerfEntry && onPerfEntry instanceof Function) {
    import('web-vitals').then(
      ({ getCLS, getFID, getFCP, getLCP, getTTFB }) => {
        getCLS(onPerfEntry);
        getFID(onPerfEntry);
        getFCP(onPerfEntry);
        getLCP(onPerfEntry);
        getTTFB(onPerfEntry);
      }
    );
  }
};

export default reportWebVitals;
```

## Purpose
- Exposes lifecycle hooks for:
  - CLS, FID, FCP, LCP, TTFB measurements.
- Enables sending metrics to analytics or console.

## Relationships
- **Imported by** `src/index.js`.
- Optional; can log to `console.log` or a remote endpoint.

---

# src/setupTests.js ⚙️
Configures Jest with additional matchers for DOM assertions.

```js
// jest-dom adds custom jest matchers for asserting on DOM nodes.
// learn more: https://github.com/testing-library/jest-dom
import '@testing-library/jest-dom';
```

## Purpose
- Extends Jest’s `expect` with `toHaveTextContent`, `toBeVisible`, etc.
- Automatically loaded by Create React App’s test runner.

## Relationships
- **Loaded** when running `npm test`.
- Supports **src/App.test.js** and any future component tests.

---

*This documentation covers the selected files and highlights how they integrate to form the overall React-based transport management application.*
