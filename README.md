struct ContentView: View {
    @StateObject private var viewModel = OTPViewModel()
        
        var body: some View {
            VStack(spacing: 20) {
                Text("Enter OTP")
                    .font(.largeTitle)
                    .fontWeight(.bold)
                
                HStack(spacing: 10) {
                    ForEach(0..<viewModel.otpCount, id: \.self) { index in
                        OTPTextField(index: index, text: "", viewModel: viewModel, isFocused: true)
//                            .onTapGesture {
//                                viewModel.currentFocus = index
//                            }
                    }
                }
                
                Button(action: viewModel.verifyOTP) {
                    Text("Verify OTP")
                        .font(.headline)
                        .foregroundColor(.white)
                        .frame(width: 200, height: 50)
                        .background(viewModel.isOTPComplete ? Color.blue : Color.gray)
                        .cornerRadius(10)
                }
                .disabled(!viewModel.isOTPComplete)
                
                if let error = viewModel.errorMessage {
                    Text(error)
                        .foregroundColor(.red)
                        .font(.footnote)
                }
            }
            .padding()
            .onAppear {
                viewModel.listenForPaste()
            }
        }
}
struct OTPTextField: View {
    let index : Int
    @Binding var text: String
    @ObservedObject var viewModel: OTPViewModel
    @FocusState private var isFocused: Bool
    
    var body: some View {
        TextField("", text: $text, onEditingChanged: {isEditing in
            if isEditing{
                viewModel.currentFocus = index
            }
        })
            .keyboardType(.numberPad)
            .multilineTextAlignment(.center)
            .frame(width: 50, height: 50)
            .background(RoundedRectangle(cornerRadius: 10).stroke(isFocused ? Color.blue : Color.gray, lineWidth: 2))
            .font(.title)
//            .onReceive(text.publisher.collect()) {
//                if let first = $0.first, $0.count > 1 {
//                    text = String(first) // Allow only one digit
//                }
//            }
            .onChange(of: viewModel.otp[index]) { newValue in
                        if newValue.count > 1 {
                            viewModel.otp[index] = String(newValue.prefix(1))
                        }
                        if !newValue.isEmpty && index < viewModel.otpCount - 1 {
                            viewModel.currentFocus = index + 1
                        }
                    }
                    .onChange(of: viewModel.currentFocus) { newFocus in
                        isFocused = (newFocus == index)
                    }
                    .onReceive(NotificationCenter.default.publisher(for: UITextField.textDidChangeNotification)) { _ in
                        if viewModel.otp[index].isEmpty, viewModel.lastDeletedIndex == index, index > 0 {
                            viewModel.otp[index - 1] = ""
                            viewModel.currentFocus = index - 1
                        }
                    }
                    .onKeyPress { key in
                        if key == .delete, viewModel.otp[index].isEmpty, index > 0 {
                            viewModel.lastDeletedIndex = index - 1
                            viewModel.currentFocus = index - 1
                        }
                    }
                }
}
class OTPViewModel: ObservableObject {
    @Published var otp: [String] = Array(repeating: "", count: 6)
    @Published var currentFocus: Int? = 0
    @Published var errorMessage: String?
    
    let otpCount = 6
    
    var isOTPComplete: Bool {
        !otp.contains("")
    }
    
    /// Function to verify the OTP (dummy logic)
    func verifyOTP() {
        let enteredOTP = otp.joined()
        if enteredOTP == "123456" { // Example valid OTP
            errorMessage = nil
            print("OTP Verified Successfully")
        } else {
            errorMessage = "Invalid OTP. Try again."
        }
    }
    
    /// Listen for OTP paste from clipboard
    func listenForPaste() {
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
            if let clipboardString = UIPasteboard.general.string, clipboardString.count == self.otpCount, clipboardString.allSatisfy({ $0.isNumber }) {
                self.otp = clipboardString.map { String($0) }
                self.currentFocus = nil // Dismiss keyboard
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
