import SwiftUI

struct ContentView: View {
    @State private var text: String = ""

    var body: some View {
        ScrollView {
            VStack(spacing: 16) {
                ForEach(1...20, id: \.self) { index in
                    Text("Item \(index)")
                        .frame(maxWidth: .infinity)
                        .padding()
                        .background(Color.blue.opacity(0.2))
                        .cornerRadius(10)
                }
                
                TextField("Enter text", text: $text)
                    .textFieldStyle(RoundedBorderTextFieldStyle())
                    .padding()
            }
            .padding()
        }
        .safeAreaInset(edge: .bottom) {
            Button(action: {
                print("Button tapped")
            }) {
                Text("Sticky Button")
                    .frame(maxWidth: .infinity)
                    .padding()
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }
            .padding()
            .background(.ultraThinMaterial) // Adds a blur effect
        }
    }
}
