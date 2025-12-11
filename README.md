# bmi-caluclator-python
It helps to calculate the BMI index of a person using height and weight
import os 
import json
from datetime import datetime

def clear_screen():
  os.system('cls' if os.name == 'nt' else 'clear')

def get_bmi(weight, height_m):
  return weight/(height_m**2)

def get_category(bmi):
  if bmi < 18.5:
    return "Underweight"
  elif bmi < 25.0:  
    return "Normal"
  elif bmi < 30.0:
    return "Overweight"
  else:
    return "Obese"

def load_history():
  try:
    with open('bmi_history.json', 'r') as f:
      return json.load(f)
  except FileNotFoundError:
    return []
  except json.JSONDecodeError:
    print("Warning: BMI history file is corrupted. Starting new history.")
    return []
  
def save_history(history):
  with open('bmi_history.json', 'w') as f:
    json.dump(history, f, indent=2)

def main():
  clear_screen()
  print("BMI Calculator - Agile Lab")
  print("="*40)

  history = load_history()
  
  while True:
    try:
      weight = float(input("Enter weight (kg): "))
      height_cm = float(input("Enter height (cm, e.g., 175): ")) 
      
      if weight <= 0 or height_cm <= 0:
        print("Weight and height must be positive values.")
        continue 
        
      height_m = height_cm / 100 
        
      bmi = get_bmi(weight, height_m)
      category = get_category(bmi)
      
      print(f"Your BMI is: **{bmi:.1f}**")
      print(f"Category: **{category}**")
      
      entry = {
        'date': datetime.now().strftime("%Y-%m-%d %H:%M"),
        'weight': weight,
        'height_cm': height_cm, 
        'bmi': round(bmi, 1),
        'category': category
      }
      
      history.insert(0, entry) 
      print("\n--- Recent Calculations ---")
      if history:
        for i, h in enumerate(history[:3], 1):
          print(f"{i}. {h['date']} | BMI: {h['bmi']} ({h['category']})")
      else:
        print("No prior history available.")
      print("---------------------------\n")
      
      cont = input("Calculate again? (y/n): ").lower()
      if cont != 'y':
        save_history(history)
        print("History saved. Goodbye!")
        break
    
    except ValueError:
      print("Error: Enter valid numbers for weight and height.")
    except KeyboardInterrupt:
      save_history(history)
      print("\nHistory saved. Goodbye!")
      break

if __name__ == "__main__":
  main()
