func validSubscriptionId() -> Bool {
        if subscriberIdText.count > 5{
            subscriberIdText = String(subscriberIdText.prefix(5))
            countCheckForSubscriberId = true
        }else{
            countCheckForSubscriberId = false
        }
        return countCheckForSubscriberId
        
    }
