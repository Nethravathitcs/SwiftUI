import SwiftUI

struct OTPView: View {
    @State private var otp: [String] = Array(repeating: "", count: 4)
    @FocusState private var focusedField: Int?

    var body: some View {
        HStack(spacing: 10) {
            ForEach(0..<4, id: \.self) { index in
                TextField("", text: Binding(
                    get: { otp[index] },
                    set: { newValue in
                        if newValue.count <= 1 {
                            otp[index] = newValue
                            if !newValue.isEmpty {
                                focusedField = index < 3 ? index + 1 : nil // Move to next field
                            }
                        } else {
                            otp[index] = String(newValue.prefix(1)) // Restrict to 1 character
                        }
                    }
                ))
                .frame(width: 50, height: 50)
                .textFieldStyle(.roundedBorder)
                .multilineTextAlignment(.center)
                .keyboardType(.numberPad)
                .focused($focusedField, equals: index)
                .onChange(of: otp[index]) { newValue in
                    if newValue.isEmpty {
                        focusedField = index > 0 ? index - 1 : nil // Move back on delete
                    }
                }
            }
        }
        .padding()
        .onAppear { focusedField = 0 } // Focus first field on appear
    }
}

struct OTPView_Previews: PreviewProvider {
    static var previews: some View {
        OTPView()
    }
}
