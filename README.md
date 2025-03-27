const express = require('express');
const cors = require('cors');
const app = express();

app.use(cors());
app.use(express.json());

function calculateTDEE({ gender, age, weight, height, activityLevel }) {
  let bmr;
  weight = parseFloat(weight);
  height = parseFloat(height);
  age = parseInt(age);
  activityLevel = parseFloat(activityLevel);

  if (gender === 'male') {
    bmr = 66 + (6.23 * weight) + (12.7 * height) - (6.8 * age);
  } else {
    bmr = 655 + (4.35 * weight) + (4.7 * height) - (4.7 * age);
  }

  const tdee = bmr * activityLevel;
  const deficit = tdee * 0.85;

  const protein = weight * 1;
  const fats = (deficit * 0.27) / 9;
  const carbs = (deficit - (protein * 4 + fats * 9)) / 4;

  return {
    bmr: bmr.toFixed(0),
    tdee: tdee.toFixed(0),
    deficit: deficit.toFixed(0),
    macros: {
      protein: protein.toFixed(0),
      fats: fats.toFixed(0),
      carbs: carbs.toFixed(0),
    },
  };
}

app.post('/api/tdee', (req, res) => {
  try {
    const result = calculateTDEE(req.body);
    res.json(result);
  } catch (error) {
    res.status(400).json({ error: 'Invalid input' });
  }
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on http://localhost:${PORT}`));
# tdee-backend
