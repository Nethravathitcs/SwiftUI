import SwiftUI

struct ActivationView: View {
    @State private var subscriberID: String = ""
    @State private var userID: String = ""
    @FocusState private var focusedField: Field?

    enum Field {
        case subscriberID, userID
    }

    var body: some View {
        ScrollViewReader { proxy in
            ScrollView {
                VStack(spacing: 20) {
                    Text("Welcome! Let's start by activating your device")
                        .font(.title2)
                        .bold()
                        .multilineTextAlignment(.center)
                        .padding()

                    Text("Check the welcome email and enter the following details")
                        .font(.body)
                        .foregroundColor(.gray)
                        .multilineTextAlignment(.center)

                    VStack(alignment: .leading) {
                        Text("Subscriber ID")
                            .font(.headline)
                        TextField("Enter subscriber ID", text: $subscriberID)
                            .textFieldStyle(RoundedBorderTextFieldStyle())
                            .focused($focusedField, equals: .subscriberID)
                            .id(Field.subscriberID)
                    }
                    .padding(.horizontal)

                    VStack(alignment: .leading) {
                        Text("User ID")
                            .font(.headline)
                        TextField("Enter user ID", text: $userID)
                            .textFieldStyle(RoundedBorderTextFieldStyle())
                            .focused($focusedField, equals: .userID)
                            .id(Field.userID)
                    }
                    .padding(.horizontal)

                    Button("Continue") {
                        // Handle continue action
                    }
                    .buttonStyle(.borderedProminent)
                    .padding()

                }
                .padding(.top, 50)
            }
            .onChange(of: focusedField) { newFocus in
                if let field = newFocus {
                    withAnimation {
                        proxy.scrollTo(field, anchor: .center)
                    }
                }
            }
        }
    }
}

struct ActivationView_Previews: PreviewProvider {
    static var previews: some View {
        ActivationView()
    }
}
