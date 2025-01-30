This is my Readme
import SwiftUI

struct CustomText: View {
    var text: String
    var font: Font
    var color: Color
    var alignment: TextAlignment
    var weight: Font.Weight

    init(
        _ text: String,
        font: Font = .body,
        color: Color = .primary,
        alignment: TextAlignment = .leading,
        weight: Font.Weight = .regular
    ) {
        self.text = text
        self.font = font
        self.color = color
        self.alignment = alignment
        self.weight = weight
    }

    var body: some View {
        Text(text)
            .font(font)
            .foregroundColor(color)
            .fontWeight(weight)
            .multilineTextAlignment(alignment)
    }
}

