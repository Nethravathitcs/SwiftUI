
import XCTest

final class SmartPassPINScreenUITests: XCTestCase {
    
    let app = XCUIApplication()
    
    override func setUp() {
        continueAfterFailure = false
        app.launch()
    }
    
    func testSmartPassPINScreenElements() {
        // Check if the title exists
        let title = app.staticTexts["Set up your Smart Pass PIN"]
        XCTAssertTrue(title.exists, "Title should be present")
        
        // Check if the Enter PIN field exists
        let enterPinField = app.textFields["enterPINField"]
        XCTAssertTrue(enterPinField.exists, "Enter PIN field should be present")
        
        // Check if the Confirm PIN field exists
        let confirmPinField = app.textFields["confirmPINField"]
        XCTAssertTrue(confirmPinField.exists, "Confirm PIN field should be present")
        
        // Check if the button exists
        let setPINButton = app.buttons["setPINButton"]
        XCTAssertTrue(setPINButton.exists, "Set PIN button should be present")
    }
    
    func testPINEntryAndValidation() {
        let enterPinField = app.textFields["enterPINField"]
        let confirmPinField = app.textFields["confirmPINField"]
        let setPINButton = app.buttons["setPINButton"]
        let errorMessage = app.staticTexts["errorMessageLabel"]
        
        // Enter mismatched PINs
        enterPinField.tap()
        enterPinField.typeText("1234")
        
        confirmPinField.tap()
        confirmPinField.typeText("5678")
        
        setPINButton.tap()
        
        // Check for error message
        XCTAssertTrue(errorMessage.exists, "Error message should be displayed for mismatched PINs")
    }
}



import SwiftUI

class SmartPassPINViewModel: ObservableObject {
    @Published var enterPIN: String = ""
    @Published var confirmPIN: String = ""
    @Published var errorMessage: String = ""
    
    func validatePIN() {
        if enterPIN.count == 4 && confirmPIN.count == 4 {
            if enterPIN == confirmPIN {
                errorMessage = ""
                print("PIN set successfully")
            } else {
                errorMessage = "PINs do not match"
            }
        } else {
            errorMessage = "Enter a 4-digit PIN"
        }
    }
}






import SwiftUI

struct SmartPassPINScreen: View {
    @StateObject private var viewModel = SmartPassPINViewModel()
    
    var body: some View {
        VStack(spacing: 20) {
            Text("Set up your Smart Pass PIN")
                .font(.title)
                .bold()
            
            Text("Choose a new 4-digit PIN to make transactions, submit requests, and access security settings.")
                .multilineTextAlignment(.center)
                .padding()
            
            SecureField("Enter PIN", text: $viewModel.enterPIN)
                .keyboardType(.numberPad)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .accessibilityIdentifier("enterPINField")
            
            SecureField("Confirm PIN", text: $viewModel.confirmPIN)
                .keyboardType(.numberPad)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .accessibilityIdentifier("confirmPINField")
            
            if !viewModel.errorMessage.isEmpty {
                Text(viewModel.errorMessage)
                    .foregroundColor(.red)
                    .accessibilityIdentifier("errorMessageLabel")
            }
            
            Button(action: viewModel.validatePIN) {
                Text("Set PIN")
                    .padding()
                    .frame(maxWidth: .infinity)
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }
            .disabled(viewModel.enterPIN.count != 4 || viewModel.confirmPIN.count != 4)
            .accessibilityIdentifier("setPINButton")
            
            Spacer()
        }
        .padding()
    }
}
