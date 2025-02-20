import SwiftUI

struct ContentView: View {
    @AppStorage("username") var username: String = "Guest"

    var body: some View {
        NavigationView {
            VStack {
                Text("Current Username: \(username)")
                    .font(.title)

                NavigationLink("Go to Profile", destination: ProfileView())
            }
            .padding()
        }
    }
}

struct ProfileView: View {
    @AppStorage("username") var username: String = "Guest"

    var body: some View {
        VStack {
            Text("Profile Username: \(username)")
                .font(.title)

            Button("Change Username") {
                username = "NewUser123"
            }
        }
        .padding()
    }
}

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
