import SwiftUI

struct ContentView: View {
    @State private var text1 = ""
    @State private var text2 = ""
    @FocusState private var focusedField: Field?
    
    enum Field {
        case text1, text2
    }
    
    @State private var keyboardHeight: CGFloat = 0

    var body: some View {
        VStack {
            ScrollViewReader { proxy in
                ScrollView {
                    VStack(spacing: 20) {
                        TextField("Enter first text", text: $text1)
                            .textFieldStyle(RoundedBorderTextFieldStyle())
                            .padding()
                            .background(Color.white)
                            .cornerRadius(10)
                            .shadow(radius: 3)
                            .focused($focusedField, equals: .text1)
                            .id(1)

                        TextField("Enter second text", text: $text2)
                            .textFieldStyle(RoundedBorderTextFieldStyle())
                            .padding()
                            .background(Color.white)
                            .cornerRadius(10)
                            .shadow(radius: 3)
                            .focused($focusedField, equals: .text2)
                            .id(2)
                    }
                    .padding()
                }
                .onChange(of: focusedField) { newField in
                    withAnimation {
                        if let newField = newField {
                            proxy.scrollTo(newField == .text1 ? 1 : 2, anchor: .top)
                        }
                    }
                }
            }

            Spacer() // Pushes the button to the bottom

            Button("Submit") {
                hideKeyboard()
            }
            .padding()
            .frame(maxWidth: .infinity)
            .background(Color.blue)
            .foregroundColor(.white)
            .cornerRadius(10)
            .padding(.bottom, keyboardHeight == 0 ? 20 : keyboardHeight) // Adjust for keyboard
        }
        .padding()
        .onAppear {
            addKeyboardObservers()
        }
        .onDisappear {
            removeKeyboardObservers()
        }
    }

    // Keyboard Height Detection
    private func addKeyboardObservers() {
        NotificationCenter.default.addObserver(forName: UIResponder.keyboardWillShowNotification,
                                               object: nil,
                                               queue: .main) { notification in
            if let keyboardFrame = notification.userInfo?[UIResponder.keyboardFrameEndUserInfoKey] as? CGRect {
                withAnimation {
                    keyboardHeight = keyboardFrame.height
                }
            }
        }
        NotificationCenter.default.addObserver(forName: UIResponder.keyboardWillHideNotification,
                                               object: nil,
                                               queue: .main) { _ in
            withAnimation {
                keyboardHeight = 0
            }
        }
    }

    private func removeKeyboardObservers() {
        NotificationCenter.default.removeObserver(self, name: UIResponder.keyboardWillShowNotification, object: nil)
        NotificationCenter.default.removeObserver(self, name: UIResponder.keyboardWillHideNotification, object: nil)
    }

    // Dismiss Keyboard
    private func hideKeyboard() {
        focusedField = nil
    }
}
