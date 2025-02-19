import SwiftUI

struct PasswordCheckerView: View {
    @State private var password: String = ""

    // Validation Checks
    var isLengthValid: Bool {
        password.count >= 12 && password.count <= 24
    }
    
    var containsUpperAndLowercase: Bool {
        let uppercaseSet = CharacterSet.uppercaseLetters
        let lowercaseSet = CharacterSet.lowercaseLetters
        return password.rangeOfCharacter(from: uppercaseSet) != nil &&
               password.rangeOfCharacter(from: lowercaseSet) != nil
    }
    
    var containsSpecialAndNumber: Bool {
        let specialCharacterSet = CharacterSet.punctuationCharacters.union(.symbols)
        let numberSet = CharacterSet.decimalDigits
        return password.rangeOfCharacter(from: specialCharacterSet) != nil &&
               password.rangeOfCharacter(from: numberSet) != nil
    }
    
    // Function to determine image based on validation
    func validationImage(for condition: Bool) -> String {
        return condition ? "checkmark.circle.fill" : "xmark.circle.fill"
    }
    
    func validationColor(for condition: Bool) -> Color {
        return condition ? .green : .red
    }

    var body: some View {
        VStack(spacing: 20) {
            SecureField("Enter Password", text: $password)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .padding()

            // Length Validation
            HStack {
                Image(systemName: validationImage(for: isLengthValid))
                    .foregroundColor(validationColor(for: isLengthValid))
                Text("12-24 characters")
            }

            // Upper & Lowercase Validation
            HStack {
                Image(systemName: validationImage(for: containsUpperAndLowercase))
                    .foregroundColor(validationColor(for: containsUpperAndLowercase))
                Text("Contains upper & lowercase letters")
            }

            // Special Character & Number Validation
            HStack {
                Image(systemName: validationImage(for: containsSpecialAndNumber))
                    .foregroundColor(validationColor(for: containsSpecialAndNumber))
                Text("Contains at least 1 special character & 1 number")
            }
        }
        .padding()
    }
}

struct PasswordCheckerView_Previews: PreviewProvider {
    static var previews: some View {
        PasswordCheckerView()
    }
}
