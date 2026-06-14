import re
import getpass

class PasswordStrengthAnalyzer:
    def __init__(self):
        # Define password policy requirements
        self.min_length = 8
        self.require_upper = True
        self.require_lower = True
        self.require_digit = True
        self.require_special = True
        self.special_chars = "!@#$%^&*()_+-=[]{}|;:,.<>?"
        
        # Common weak passwords (simplified list for demo)
        self.common_passwords = [
            "password", "123456", "12345678", "qwerty", "abc123",
            "monkey", "letmein", "dragon", "111111", "baseball",
            "iloveyou", "trustno1", "sunshine", "master", "hello",
            "football", "jesus", "ninja", "mustang", "password1"
        ]

    def check_strength(self, password):
        """Analyze password strength and return feedback"""
        feedback = []
        score = 0
        max_score = 5  # Adjust based on your policy
        
        # 1. Check against common passwords
        if password.lower() in self.common_passwords:
            feedback.append("❌ This is a very common password - choose something more unique.")
            return "Very Weak", feedback, 1
        
        # 2. Length check
        length = len(password)
        if length < self.min_length:
            feedback.append(f"❌ Password should be at least {self.min_length} characters long (currently {length}).")
        elif length < 12:
            feedback.append(f"⚠️ Consider using a longer password (at least 12 characters) for better security.")
            score += 1
        else:
            feedback.append(f"✅ Good length ({length} characters).")
            score += 2
        
        # 3. Complexity checks
        checks = {
            'uppercase': (re.search(r'[A-Z]', password), "Uppercase letters"),
            'lowercase': (re.search(r'[a-z]', password), "Lowercase letters"),
            'digits': (re.search(r'\d', password), "Digits"),
            'special': (re.search(f'[{re.escape(self.special_chars)}]', password), "Special characters")
        }
        
        complexity_met = 0
        for check_type, (has_char, name) in checks.items():
            if has_char:
                feedback.append(f"✅ Contains {name}.")
                complexity_met += 1
            else:
                feedback.append(f"❌ Missing {name}.")
        
        if complexity_met == 4:
            score += 2
        elif complexity_met >= 2:
            score += 1
        
        # 4. Determine overall strength
        if score <= 2:
            strength = "Weak"
            feedback.append("\n🔒 Tips to improve:")
            feedback.append("- Use a mix of letters, numbers, and special characters")
            feedback.append("- Avoid using personal information or common words")
            feedback.append("- Consider using a passphrase (multiple words)")
        elif score <= 3:
            strength = "Moderate"
            feedback.append("\n🔒 Additional tips:")
            feedback.append("- Make it longer (12+ characters)")
            feedback.append("- Avoid repeating characters or patterns")
        else:
            strength = "Strong"
            feedback.append("\n🔒 Excellent! This is a strong password.")
        
        return strength, feedback, score

    def run(self):
        """Run the password analyzer"""
        print("🔐 Password Strength Analyzer 🔐")
        print("Enter 'quit' to exit\n")
        
        while True:
            password = getpass.getpass("Enter password to check: ")
            
            if password.lower() == 'quit':
                print("\nGoodbye!")
                break
                
            if not password:
                print("Please enter a password.\n")
                continue
                
            strength, feedback, score = self.check_strength(password)
            
            print("\nPassword Strength:", strength)
            print("Score:", f"{score}/5")
            print("\nFeedback:")
            for line in feedback:
                print(line)
            
            # If password is weak, offer to generate a strong one
            if score <= 2:
                print("\nWould you like a strong password suggestion? (y/n)")
                choice = input("> ").lower()
                if choice == 'y':
                    strong_pass = self.generate_strong_password()
                    print("\nSuggested strong password:", strong_pass)
            
            print("\n" + "="*50 + "\n")

    def generate_strong_password(self):
        """Generate a strong password suggestion"""
        import random
        import string
        
        # Combine all character sets
        all_chars = (
            string.ascii_letters + 
            string.digits + 
            self.special_chars
        )
        
        # Ensure at least one of each required type
        password = [
            random.choice(string.ascii_uppercase),
            random.choice(string.ascii_lowercase),
            random.choice(string.digits),
            random.choice(self.special_chars)
        ]
        
        # Fill the rest with random characters
        for _ in range(12 - len(password)):  # 12 characters total
            password.append(random.choice(all_chars))
        
        # Shuffle the result
        random.shuffle(password)
        
        return ''.join(password)

if __name__ == "__main__":
    analyzer = PasswordStrengthAnalyzer()
    analyzer.run()
