import XCTest
@testable import YourApp // Replace 'YourApp' with your actual module name

final class SmartPassPINViewModelTests: XCTestCase {
    
    var viewModel: SmartPassPINViewModel!

    override func setUp() {
        super.setUp()
        viewModel = SmartPassPINViewModel()
    }

    override func tearDown() {
        viewModel = nil
        super.tearDown()
    }

    func testEnterPIN_ShouldBeExactlyFourDigits() {
        viewModel.enterPIN = "12" // Less than 4 digits
        viewModel.confirmPIN = "12"
        viewModel.validatePIN()
        XCTAssertEqual(viewModel.errorMessage, "PIN must be 4 digits long")
    }

    func testConfirmPIN_ShouldMatchEnterPIN() {
        viewModel.enterPIN = "1234"
        viewModel.confirmPIN = "5678"
        viewModel.validatePIN()
        XCTAssertEqual(viewModel.errorMessage, "PINs do not match")
    }

    func testValidPIN_ShouldPassWithoutErrors() {
        viewModel.enterPIN = "1234"
        viewModel.confirmPIN = "1234"
        viewModel.validatePIN()
        XCTAssertEqual(viewModel.errorMessage, "") // No error message expected
    }

    func testEmptyPIN_ShouldShowError() {
        viewModel.enterPIN = ""
        viewModel.confirmPIN = ""
        viewModel.validatePIN()
        XCTAssertEqual(viewModel.errorMessage, "PIN must be 4 digits long")
    }
}
