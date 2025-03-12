import SwiftUI

struct ContentView: View {
    var body: some View {
        VStack {
            ScrollView {
                VStack(spacing: 20) {
                    ForEach(1...50, id: \.self) { index in
                        Text("Item \(index)")
                            .frame(maxWidth: .infinity)
                            .padding()
                            .background(Color.gray.opacity(0.2))
                            .cornerRadius(10)
                    }
                }
                .padding(.bottom, 60) // Prevents last item from being covered
            }
            
            // Sticky Button
            Button(action: {
                print("Button Tapped!")
            }) {
                Text("Sticky Button")
                    .frame(maxWidth: .infinity)
                    .padding()
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .cornerRadius(10)
            }
            .padding()
            .background(Color.white) // Background to make it visible
            .shadow(radius: 3)
        }
    }
}
