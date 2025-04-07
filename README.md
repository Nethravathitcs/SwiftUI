import SwiftUI

struct ContentView: View {
    @State private var firstText: String = ""
    @State private var secondText: String = ""
    
    @FocusState private var focusField: Field?
    
    enum Field: Hashable {
        case firstTextField, secondTextField
    }
    
    var body: some View {
        VStack {
            TextField("Enter first text", text: $firstText)
                .focused($focusField, equals: .firstTextField)
                .submitLabel(.next) // Set the return key to "Next"
                .onSubmit {
                    // Move focus to the second text field when "Next" is pressed
                    focusField = .secondTextField
                }
                .textFieldStyle(.roundedBorder)
                .padding()
            
            TextField("Enter second text", text: $secondText)
                .focused($focusField, equals: .secondTextField)
                .submitLabel(.done) // Set the return key to "Done"
                .onSubmit {
                    // You can perform an action here, for example, saving data
                    print("Second text: \(secondText)")
                    focusField = nil // Dismiss the keyboard
                }
                .textFieldStyle(.roundedBorder)
                .padding()
            
            Spacer()
        }
        .padding()
        .onAppear {
            // Optionally, set initial focus to the first text field when the view appears
            focusField = .firstTextField
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
