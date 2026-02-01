# iOS 26 + Foundation Models Skill Examples

## Example 1: Foundation Models Classification Service

**User:** "Create a Foundation Models service to classify user notes as tasks, ideas, or general notes"

**Expected Response:**
```swift
import FoundationModels
import Foundation

// MARK: - Classification Response

@Generable
struct NoteClassificationResponse: Codable, Sendable {
    @Guide(description: "Primary category: 'task' for actionable items, 'idea' for creative thoughts, 'note' for general observations")
    var category: String

    @Guide(description: "Priority level: 'high' for urgent tasks, 'medium' for normal items, 'low' for reference material")
    var priority: String

    @Guide(description: "Suggested tags (lowercase, hyphen-separated for multi-word)")
    var suggestedTags: [String]

    @Guide(description: "Emotional sentiment from -1.0 (negative) to +1.0 (positive). Use 0.0 for neutral factual content.")
    var sentiment: Double
}

// MARK: - Classification Service

@MainActor
class NoteClassifier {
    private var session: LanguageModelSession?

    init() {
        initialize()
    }

    private func initialize() {
        guard LanguageModelSession.isAvailable else {
            NSLog("⚠️ Foundation Models unavailable - classification disabled")
            return
        }

        session = LanguageModelSession(
            instructions: """
            You are a note classification assistant.

            Category Guidelines:
            - 'task': Actionable items with clear next steps
            - 'idea': Creative thoughts, brainstorming, future concepts
            - 'note': General observations, facts, references

            Priority Guidelines:
            - 'high': Urgent, time-sensitive tasks
            - 'medium': Normal priority items
            - 'low': Reference material, someday-maybe items

            Sentiment Guidelines:
            - NEGATIVE (-0.3 to -1.0): Frustration, stress, concern
            - NEUTRAL (-0.2 to +0.2): Factual, objective content
            - POSITIVE (+0.3 to +1.0): Excitement, gratitude, joy

            Tag Guidelines:
            - Use hyphens for multi-word: "project-management"
            - Lowercase only
            - 3-5 relevant tags maximum
            """
        )
    }

    func classify(_ text: String) async throws -> NoteClassificationResponse {
        guard let session = session else {
            throw ClassificationError.modelUnavailable
        }

        let response = try await session.respond(
            to: text,
            generating: NoteClassificationResponse.self
        )

        return response.content
    }
}

enum ClassificationError: Error {
    case modelUnavailable
}
```

---

## Example 2: HealthKit State of Mind Integration

**User:** "Add HealthKit State of Mind tracking to my context gathering service"

**Expected Response:**
```swift
import HealthKit

// MARK: - State of Mind Snapshot

struct StateOfMindSnapshot: Codable, Sendable {
    let valence: Double
    let classification: ValenceClassification
    let labels: [String]
    let associations: [String]

    enum ValenceClassification: String, Codable, CaseIterable {
        case veryUnpleasant = "very_unpleasant"
        case slightlyUnpleasant = "slightly_unpleasant"
        case neutral
        case slightlyPleasant = "slightly_pleasant"
        case veryPleasant = "very_pleasant"

        static func from(valence: Double) -> ValenceClassification {
            switch valence {
            case ..<(-0.6): return .veryUnpleasant
            case -0.6 ..< -0.2: return .slightlyUnpleasant
            case -0.2 ... 0.2: return .neutral
            case 0.2 ... 0.6: return .slightlyPleasant
            default: return .veryPleasant
            }
        }
    }
}

// MARK: - HealthKit Service

actor HealthKitService {
    private let healthStore = HKHealthStore()
    private var isAuthorized = false

    // Request authorization for State of Mind
    @available(iOS 18.0, *)
    func requestAuthorization() async throws {
        guard HKHealthStore.isHealthDataAvailable() else {
            throw HealthKitError.unavailable
        }

        let stateOfMindType = HKObjectType.stateOfMindType()

        try await healthStore.requestAuthorization(
            toShare: [],
            read: [stateOfMindType]
        )

        isAuthorized = true
    }

    // Get most recent State of Mind (last hour)
    @available(iOS 18.0, *)
    func getStateOfMind() async -> StateOfMindSnapshot? {
        guard isAuthorized else { return nil }

        let stateOfMindType = HKObjectType.stateOfMindType()
        let oneHourAgo = Date().addingTimeInterval(-3600)

        let predicate = HKQuery.predicateForSamples(
            withStart: oneHourAgo,
            end: Date(),
            options: .strictStartDate
        )

        let sortDescriptor = NSSortDescriptor(
            key: HKSampleSortIdentifierStartDate,
            ascending: false
        )

        do {
            let samples: [HKSample] = try await withCheckedThrowingContinuation { continuation in
                let query = HKSampleQuery(
                    sampleType: stateOfMindType,
                    predicate: predicate,
                    limit: 1,
                    sortDescriptors: [sortDescriptor]
                ) { _, samples, error in
                    if let error = error {
                        continuation.resume(throwing: error)
                    } else {
                        continuation.resume(returning: samples ?? [])
                    }
                }

                healthStore.execute(query)
            }

            guard let stateOfMind = samples.first as? HKStateOfMind else {
                return nil
            }

            // Map labels to strings
            let labels = stateOfMind.labels.map { label -> String in
                switch label {
                case .anxious: return "anxious"
                case .calm: return "calm"
                case .content: return "content"
                case .disappointed: return "disappointed"
                case .discouraged: return "discouraged"
                case .frustrated: return "frustrated"
                case .grateful: return "grateful"
                case .happy: return "happy"
                case .irritated: return "irritated"
                case .sad: return "sad"
                case .stressed: return "stressed"
                case .worried: return "worried"
                @unknown default: return "unknown"
                }
            }

            // Map associations to strings
            let associations = stateOfMind.associations.map { association -> String in
                switch association {
                case .community: return "community"
                case .currentEvents: return "current-events"
                case .dating: return "dating"
                case .education: return "education"
                case .family: return "family"
                case .fitness: return "fitness"
                case .friends: return "friends"
                case .health: return "health"
                case .hobbies: return "hobbies"
                case .identity: return "identity"
                case .money: return "money"
                case .partner: return "partner"
                case .selfCare: return "self-care"
                case .spirituality: return "spirituality"
                case .tasks: return "tasks"
                case .travel: return "travel"
                case .weather: return "weather"
                case .work: return "work"
                @unknown default: return "unknown"
                }
            }

            return StateOfMindSnapshot(
                valence: stateOfMind.valence,
                classification: StateOfMindSnapshot.ValenceClassification.from(
                    valence: stateOfMind.valence
                ),
                labels: labels,
                associations: associations
            )
        } catch {
            NSLog("⚠️ Failed to fetch State of Mind: \(error)")
            return nil
        }
    }
}

enum HealthKitError: Error {
    case unavailable
    case unauthorized
}
```

---

## Example 3: Swift Charts Dashboard

**User:** "Create a Swift Charts dashboard showing activity trends"

**Expected Response:**
```swift
import SwiftUI
import Charts

// MARK: - Data Models

struct ActivityDataPoint: Identifiable {
    let id = UUID()
    let date: Date
    let count: Int
}

struct SentimentDataPoint: Identifiable {
    let id = UUID()
    let date: Date
    let sentiment: Double
}

// MARK: - Dashboard View

struct AnalyticsDashboard: View {
    let activityData: [ActivityDataPoint]
    let sentimentData: [SentimentDataPoint]

    var body: some View {
        ScrollView {
            VStack(spacing: 20) {
                activityChart
                sentimentChart
            }
            .padding()
        }
        .navigationTitle("Analytics")
    }

    // Activity bar chart
    private var activityChart: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Image(systemName: "chart.bar.fill")
                    .foregroundStyle(.blue)
                Text("Activity Over Time")
                    .font(.headline)
            }

            Chart(activityData) { point in
                BarMark(
                    x: .value("Date", point.date, unit: .day),
                    y: .value("Count", point.count)
                )
                .foregroundStyle(Color.blue.gradient)
            }
            .frame(height: 200)
            .chartXAxis {
                AxisMarks(values: .stride(by: .day)) { _ in
                    AxisGridLine()
                    AxisValueLabel(format: .dateTime.day().month())
                }
            }
            .chartYAxis {
                AxisMarks(position: .leading)
            }
        }
        .padding()
        .background(Color(.secondarySystemBackground))
        .cornerRadius(12)
    }

    // Sentiment line chart
    private var sentimentChart: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Image(systemName: "chart.line.uptrend.xyaxis")
                    .foregroundStyle(.purple)
                Text("Sentiment Trends")
                    .font(.headline)
            }

            Chart(sentimentData) { point in
                LineMark(
                    x: .value("Date", point.date, unit: .day),
                    y: .value("Sentiment", point.sentiment)
                )
                .foregroundStyle(Color.purple.gradient)
                .interpolationMethod(.catmullRom)

                AreaMark(
                    x: .value("Date", point.date, unit: .day),
                    y: .value("Sentiment", point.sentiment)
                )
                .foregroundStyle(
                    LinearGradient(
                        colors: [.purple.opacity(0.3), .purple.opacity(0.1)],
                        startPoint: .top,
                        endPoint: .bottom
                    )
                )
                .interpolationMethod(.catmullRom)

                // Zero reference line
                RuleMark(y: .value("Neutral", 0.0))
                    .foregroundStyle(.gray.opacity(0.3))
                    .lineStyle(StrokeStyle(dash: [5, 5]))
            }
            .frame(height: 200)
            .chartYScale(domain: -1.0...1.0)
            .chartXAxis {
                AxisMarks(values: .stride(by: .day)) { _ in
                    AxisGridLine()
                    AxisValueLabel(format: .dateTime.day().month())
                }
            }
            .chartYAxis {
                AxisMarks(position: .leading) { value in
                    AxisGridLine()
                    AxisValueLabel {
                        if let sentiment = value.as(Double.self) {
                            Text(String(format: "%.1f", sentiment))
                        }
                    }
                }
            }
        }
        .padding()
        .background(Color(.secondarySystemBackground))
        .cornerRadius(12)
    }
}

// MARK: - Preview

#Preview {
    NavigationStack {
        AnalyticsDashboard(
            activityData: [
                ActivityDataPoint(date: Date().addingTimeInterval(-86400 * 6), count: 5),
                ActivityDataPoint(date: Date().addingTimeInterval(-86400 * 5), count: 8),
                ActivityDataPoint(date: Date().addingTimeInterval(-86400 * 4), count: 3),
                ActivityDataPoint(date: Date().addingTimeInterval(-86400 * 3), count: 12),
                ActivityDataPoint(date: Date().addingTimeInterval(-86400 * 2), count: 7),
                ActivityDataPoint(date: Date().addingTimeInterval(-86400 * 1), count: 9),
                ActivityDataPoint(date: Date(), count: 4)
            ],
            sentimentData: [
                SentimentDataPoint(date: Date().addingTimeInterval(-86400 * 6), sentiment: 0.3),
                SentimentDataPoint(date: Date().addingTimeInterval(-86400 * 5), sentiment: 0.5),
                SentimentDataPoint(date: Date().addingTimeInterval(-86400 * 4), sentiment: -0.2),
                SentimentDataPoint(date: Date().addingTimeInterval(-86400 * 3), sentiment: 0.7),
                SentimentDataPoint(date: Date().addingTimeInterval(-86400 * 2), sentiment: 0.4),
                SentimentDataPoint(date: Date().addingTimeInterval(-86400 * 1), sentiment: 0.6),
                SentimentDataPoint(date: Date(), sentiment: 0.2)
            ]
        )
    }
}
```

---

## Example 4: StoreKit 2 Subscription Manager

**User:** "Set up StoreKit 2 with monthly and annual subscriptions"

**Expected Response:**
```swift
import StoreKit
import Observation

// MARK: - Subscription Products

enum SubscriptionProduct: String, CaseIterable {
    case proMonthly = "com.app.pro.monthly"
    case proAnnual = "com.app.pro.annual"

    var displayName: String {
        switch self {
        case .proMonthly: return "Pro Monthly"
        case .proAnnual: return "Pro Annual"
        }
    }

    var tier: SubscriptionTier {
        return .pro
    }
}

enum SubscriptionTier: String, Codable {
    case free
    case pro

    var displayName: String {
        switch self {
        case .free: return "Free"
        case .pro: return "Pro"
        }
    }
}

// MARK: - Subscription Status

struct SubscriptionStatus {
    let tier: SubscriptionTier
    let expirationDate: Date?
    let isActive: Bool
    let productId: String?

    static let free = SubscriptionStatus(
        tier: .free,
        expirationDate: nil,
        isActive: true,
        productId: nil
    )
}

// MARK: - Subscription Manager

@MainActor
@Observable
class SubscriptionManager {
    private(set) var status: SubscriptionStatus = .free
    private(set) var products: [Product] = []
    private(set) var isLoading = false
    private(set) var purchaseError: Error?

    private var transactionListener: _Concurrency.Task<Void, Error>?

    static let shared = SubscriptionManager()

    private init() {
        // Load products and status
        _Concurrency.Task {
            await loadProducts()
            await updateSubscriptionStatus()
        }

        // Start listening for transactions
        transactionListener = listenForTransactions()
    }

    // MARK: - Product Loading

    func loadProducts() async {
        isLoading = true

        do {
            let productIds = SubscriptionProduct.allCases.map { $0.rawValue }
            products = try await Product.products(for: productIds)
            NSLog("✅ Loaded \(products.count) products")
        } catch {
            NSLog("❌ Failed to load products: \(error)")
            purchaseError = error
        }

        isLoading = false
    }

    // MARK: - Purchase

    func purchase(_ product: Product) async throws {
        isLoading = true

        let result = try await product.purchase()

        switch result {
        case .success(let verification):
            let transaction = try Self.checkVerified(verification)
            await updateSubscriptionStatus()
            await transaction.finish()
            NSLog("✅ Purchase successful: \(product.id)")

        case .userCancelled:
            NSLog("ℹ️ User cancelled purchase")

        case .pending:
            NSLog("ℹ️ Purchase pending")

        @unknown default:
            break
        }

        isLoading = false
    }

    // MARK: - Restore

    func restorePurchases() async {
        isLoading = true
        await AppStore.sync()
        await updateSubscriptionStatus()
        isLoading = false
        NSLog("✅ Purchases restored")
    }

    // MARK: - Transaction Verification

    static func checkVerified<T>(_ result: VerificationResult<T>) throws -> T {
        switch result {
        case .unverified:
            throw StoreError.failedVerification
        case .verified(let safe):
            return safe
        }
    }

    // MARK: - Status Update

    func updateSubscriptionStatus() async {
        var currentStatus: SubscriptionStatus = .free

        for await result in Transaction.currentEntitlements {
            do {
                let transaction = try Self.checkVerified(result)

                if let product = SubscriptionProduct(rawValue: transaction.productID) {
                    currentStatus = SubscriptionStatus(
                        tier: product.tier,
                        expirationDate: transaction.expirationDate,
                        isActive: true,
                        productId: transaction.productID
                    )
                    break
                }
            } catch {
                NSLog("⚠️ Failed to verify transaction: \(error)")
            }
        }

        status = currentStatus
        NSLog("ℹ️ Subscription status: \(status.tier.displayName)")
    }

    // MARK: - Transaction Listener

    private func listenForTransactions() -> _Concurrency.Task<Void, Error> {
        _Concurrency.Task { @MainActor [weak self] in
            guard let self = self else { return }

            for await result in Transaction.updates {
                do {
                    let transaction = try Self.checkVerified(result)
                    await self.updateSubscriptionStatus()
                    await transaction.finish()
                } catch {
                    NSLog("⚠️ Transaction verification failed: \(error)")
                }
            }
        }
    }
}

// MARK: - Store Error

enum StoreError: Error {
    case failedVerification
}
```

---

## Example 5: Fix Swift 6 Concurrency Error

**User:** "I'm getting 'main actor-isolated property cannot be referenced from a nonisolated context'"

**Expected Response:**

This error occurs when trying to access a `@MainActor` property from outside the main actor context. Here are the common fixes:

**Option 1: Make the method @MainActor**
```swift
// ❌ Before
func doWork() {
    self.mainActorProperty = value  // Error!
}

// ✅ After
@MainActor
func doWork() {
    self.mainActorProperty = value  // Works!
}
```

**Option 2: Use Task with @MainActor closure**
```swift
// ❌ Before
Task.detached {
    self.mainActorProperty = value  // Error!
}

// ✅ After
_Concurrency.Task { @MainActor in
    self.mainActorProperty = value  // Works!
}
```

**Option 3: Make the method static if it doesn't need instance state**
```swift
// ❌ Before (instance method)
@MainActor
class MyClass {
    func checkVerified(_ result: VerificationResult) throws -> Transaction {
        // Can't call from non-MainActor context
    }
}

// ✅ After (static method)
@MainActor
class MyClass {
    static func checkVerified(_ result: VerificationResult) throws -> Transaction {
        // Can call from anywhere!
    }
}
```

**Option 4: Use nonisolated if the property doesn't actually need MainActor**
```swift
@MainActor
class MyClass {
    nonisolated let config: Configuration  // Can access from anywhere
    var state: AppState  // Requires MainActor
}
```

The key is understanding:
- `@MainActor` properties can only be accessed from MainActor context
- Use `_Concurrency.Task` not `Task` to avoid namespace conflicts
- Static methods bypass actor isolation checks
- `nonisolated` opts out of actor isolation for specific members
