# React Typeahead Search

```jsx
import { useState } from "react";

const allCities = [
  {
    city: "New York",
    val: "www.newyork.com",
  },
  {
    city: "New York City",
    val: "www.newyorkcity.com",
  },
  {
    city: "Rome",
    val: "www.rome.com",
  },
  {
    city: "RomeCity",
    val: "www.romecity.com",
  },
];

export default function App() {
  const [cities, setCities] = useState([]);
  const [selectedCity, setSelectedCity] = useState({});

  const handleChange = (e) => {
    console.log(e.target.value);
    input = e.target.value;

    if (input.trim()) {
      try {
        // Create a regex to match city names starting with the input
        const regex = new RegExp(`^${input}`, "i");
        const filteredCities = allCities.filter((city) =>
          regex.test(city.city)
        );
        setCities(filteredCities);
      } catch (err) {
        console.error("Invalid regex input:", err.message);
        setCities([]);
      }
    } else {
      setCities([]);
    }
  };

  return (
    <div className="App">
      <input onChange={(e) => handleChange(e)} />
      <ul>
        {cities.map((city) => {
          return (
            <li key={city.city}>
              {city.city}
              <button onClick={() => setSelectedCity(city)}>set city</button>
            </li>
          );
        })}
      </ul>
      <div>Selected city</div>
      {selectedCity && (
        <div>
          {selectedCity.city}, {selectedCity.val}{" "}
        </div>
      )}
    </div>
  );
}

```
