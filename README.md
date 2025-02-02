import SwiftUI
import Combine
 
struct OTPInputView: View {
    @State public var otp: [String] = Array(repeating: "", count: 6)
    @FocusState public var currentIndex: Int? // Use FocusState for managing focus
    @State public var timerValue = 10
    @State public var isResendEnabled = false
    @State public var timerCancellable: Cancellable?
    
    var onCompleted: (String) -> Void
    var onResend: () -> Void // Resend callback
    
    var body: some View {
        VStack(spacing: 20) {
            // OTP Inputs
            HStack(spacing: 12) {
                ForEach(0..<6, id: \.self) { index in
                    TextField("", text: $otp[index])
                        .frame(width: 50, height: 50)
                        .background(currentIndex == index ? Color.blue.opacity(0.3) : Color.gray.opacity(0.1)) // Focus color change
                        .cornerRadius(8)
                        .multilineTextAlignment(.center)
                        .keyboardType(.numberPad)
                        .onChange(of: otp[index]) { newValue in
                            // Properly handle changes for each OTP field
                            handleOTPChange(for: index, newValue: newValue)
                        }
                        .focused($currentIndex, equals: index) // Use @FocusState here
                        .onTapGesture {
                            // Lock focus to middle: Only allow focusing on non-empty fields
                            if !otp[index].isEmpty {
                                currentIndex = index
                            }
                        }
                }
            }
            
            // Timer View
            Text("\(timerValue):00")
                .font(.headline)
                .foregroundColor(.blue)
            
            // Resend Button
            Button(action: resendOTP) {
                Text("Resend OTP")
                    .foregroundColor(.white)
                    .padding()
                    .background(isResendEnabled ? Color.blue : Color.gray)
                    .cornerRadius(8)
            }
            .disabled(!isResendEnabled)
        }
        .padding()
        .onAppear {
            startTimer()
        }
        .onReceive(NotificationCenter.default.publisher(for: UIResponder.keyboardWillHideNotification)) { _ in
            // Detect Delete key press (the backspace key in a custom numeric keyboard)
            handleDeleteKeyPress()
        }
    }
    
    // Handle OTP input and focus movement
    public func handleOTPChange(for index: Int, newValue: String) {
        // If user tries to enter more than one character, take the last one
        if newValue.count > 1 {
            otp[index] = String(newValue.last!)
        }
        
        // Move focus to the next empty field after entering a value
        if newValue.count == 1 {
            if let nextEmptyIndex = otp.firstIndex(of: "") {
                currentIndex = nextEmptyIndex
            }
        }
        
        // Handle delete behavior: Move focus to the last filled field and delete from there
        if newValue.isEmpty {
            // Find the last filled field
            if let lastFilledIndex = otp.lastIndex(where: { !$0.isEmpty }) {
                currentIndex = lastFilledIndex
            }
        }
        
        // If all OTP fields are filled, trigger the completion callback
        let enteredOTP = otp.joined()
        if enteredOTP.count == otp.count {
            onCompleted(enteredOTP)
            
        }
    }
    
    // Handle delete key press to delete OTP value
    public func handleDeleteKeyPress() {
        if let currentIndex = currentIndex {
            // If the current field has a value, delete it and shift focus
            if !otp[currentIndex].isEmpty {
                otp[currentIndex] = "" // Clear the current field value
                moveFocusToLastFilledField()
            }
        }
    }
    
    // Move focus to the last filled OTP field
    public func moveFocusToLastFilledField() {
        // Find the last filled field
        if let lastFilledIndex = otp.lastIndex(where: { !$0.isEmpty }) {
            self.currentIndex = lastFilledIndex
        }
    }
    
    // Start the timer countdown
    public func startTimer() {
        timerCancellable?.cancel() // Cancel previous timer if any
        timerValue = 10
        isResendEnabled = false
        timerCancellable = Timer.publish(every: 1, on: .main, in: .common)
            .autoconnect()
            .sink { _ in
                if self.timerValue > 0 {
                    self.timerValue -= 1
                } else {
                    // Set resend button to enabled immediately when timer reaches 0
                    self.isResendEnabled = true
                    self.timerCancellable?.cancel() // Stop the timer
                }
            }
    }
    
    // Handle OTP resend with callback
    public func resendOTP() {
        onResend()    // Trigger the resend callback (e.g., API call for a new OTP)
        startTimer() // Restart the timer when OTP is resent
        print("OTP resent.")
    }
}
 
struct OTPInputView_Previews: PreviewProvider {
    static var previews: some View {
        OTPInputView(
            onCompleted: { otp in
                print("OTP entered: \(otp)")
            },
            onResend: {
                // Simulate network call for resending OTP
                print("Resend OTP request initiated.")
            }
        )
    }
}
