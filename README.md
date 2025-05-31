npm install react-bootstrap bootstrap
import 'bootstrap/dist/css/bootstrap.min.css';
import { useEffect, useState } from "react";

function Clock({ timeZone }) {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime(new Date());
    }, 1000);
    return () => clearInterval(intervalId);
  }, []);

  return (
    <div>
      <h5>{time.toLocaleTimeString("en-US", { timeZone })}</h5>
    </div>
  );
}

export default Clock;
import { Container, Row, Col, Card } from "react-bootstrap";
import Clock from "./Clock";

function WorldClock({ clockData }) {
  return (
    <Container className="mt-4">
      <Row>
        {clockData.map(({ city, zone }) => (
          <Col key={zone} md={4} className="mb-3">
            <Card>
              <Card.Body>
                <Card.Title>{city}</Card.Title>
                <Clock timeZone={zone} />
              </Card.Body>
            </Card>
          </Col>
        ))}
      </Row>
    </Container>
  );
}

export default WorldClock;
import WorldClock from "./WorldClock";

function App() {
  const clockData = [
    { city: "New York", zone: "America/New_York" },
    { city: "London", zone: "Europe/London" },
    { city: "Tokyo", zone: "Asia/Tokyo" },
    { city: "Sydney", zone: "Australia/Sydney" },
    { city: "Dubai", zone: "Asia/Dubai" },
  ];

  return (
    <div>
      <h1 className="text-center mt-4">World Clock</h1>
      <WorldClock clockData={clockData} />
    </div>
  );
}

export default App;
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import "bootstrap/dist/css/bootstrap.min.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
npm install
npm run dev

