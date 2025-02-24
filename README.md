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
