import SwiftUI

struct ContentView: View {
    @State private var text: String = ""

    var body: some View {
        TextField("Enter text", text: $text)
            .textFieldStyle(RoundedBorderTextFieldStyle())
            .padding()
            .onChange(of: text) { newValue in
                let regex = "^[a-zA-Z0-9]*$" // Only letters and numbers
                if newValue.range(of: regex, options: .regularExpression) == nil {
                    text = String(text.dropLast()) // Remove last entered invalid character
                }
            }
    }
}
