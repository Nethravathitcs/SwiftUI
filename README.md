import SwiftUI

struct OTPView: View {
    let otpLength = 4  // Change this for different OTP lengths
    @State private var otp: [String] = Array(repeating: "", count: 4)
    @FocusState private var focusedField: Int?

    var body: some View {
        HStack(spacing: 12) {
            ForEach(0..<otpLength, id: \.self) { index in
                TextField("", text: Binding(
                    get: { otp[index] },
                    set: { newValue in
                        handleInput(index: index, newValue: newValue)
                    }
                ))
                .frame(width: 50, height: 50)
                .background(RoundedRectangle(cornerRadius: 8).stroke(Color.gray, lineWidth: 1))
                .multilineTextAlignment(.center)
                .keyboardType(.numberPad)
                .focused($focusedField, equals: index)
                .onChange(of: otp[index]) { newValue in
                    if newValue.isEmpty {
                        moveBackward(index)
                    }
                }
            }
        }
        .padding()
        .onAppear { focusedField = 0 } // Auto-focus the first field
    }

    private func handleInput(index: Int, newValue: String) {
        if newValue.count == 1 { // Allow only one digit
            otp[index] = newValue
            moveForward(index)
        } else if newValue.isEmpty { // Handle deletion
            otp[index] = ""
            moveBackward(index)
        }
    }

    private func moveForward(_ index: Int) {
        if index < otpLength - 1 {
            focusedField = index + 1
        } else {
            focusedField = nil // Dismiss keyboard if last field is filled
        }
    }

    private func moveBackward(_ index: Int) {
        if index > 0 {
            focusedField = index - 1
        }
    }
}

struct OTPView_Previews: PreviewProvider {
    static var previews: some View {
        OTPView()
    }
}
