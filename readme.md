# Botshop SwiftUI

Start a new Xcode project. Choose: 

- File > New > Project 

Then Select iOS App

Choose interface SwiftUI. 

## Create the Tab View 

Create a new SwiftUI File. Choose File > New > File... 

Select SwiftUI View from Interface. Name this MainView. 

This will be the main view and tabbed view controller. 

Botshop is made of two lists. One list is a Collection View with a grid of products. The other is a list view with a list of orders. 

Create these two views. 

Create a new SwiftUI View. File > New. Choose SwiftUI View from Interface. Name this ProductsView. 

Change the name default text to read: "Products List"

Create a new SwiftUI View. File > New. Choose SwiftUI View from Interface. Name this OrdersList.

Change the default text to read "Orders List"

In MainView you will display the ProductsView and OrderList. 

Update MainView to look like this: 

```Swift 
struct MainView: View {
	var body: some View {
	TabView {
		ProductsView()
			.tabItem {
				Label("Product", systemImage: "list.dash")
			}
		OrdersList()
			.tabItem {
				Label("History", image: "square.pencil")
			}
		}
	}
}
```

This should display the two new views you created, each in its tab. 

`tabItem` should display the label and icon in the tab at the bottom of the screen. 

## Setting up the data source

For this project, you will create a Singleton to act as the data store. This will provide two lists of objects to the rest of the app. 

`Item` objects will store product items and `Oder`s will store a list of orders. 

Add a new Swift File: File > New File... Then choose Swift File. Set the name to Item.swift. 

Add the following: 

```Swift 
import Foundation
import UIKit

struct Item: Identifiable {
	var id: UUID
	var title: String
	var image: UIImage

	
	init(title: String, image: UIImage) {
		self.id = UUID()
		self.title = title
		self.image = image
	}
}
```

This is a struct that is identifiable. It has two properties: title and image which are the main data it stores. It also has the id property to conform to the identifiable protocol. 

Create a new Swift file: File > New File..., choose Swift File. Name this Order.swft.

Add the following: 

```Swift 
import Foundation
import UIKit

struct Order: Identifiable {
	var id: UUID
	var title: String
	var image: UIImage
	
	var items: [Item] = []
	
	init(title: String, image: UIImage, items: [Item]) {
		self.id = UUID()
		self.title = title
		self.image = image
		self.items = items
	}
}
```

This is the same as Item with the addition of an items property which stores an array of items. With this, an order has a name and image and a list of items that can be part of the order. 

Create a new Swift file: File > New File..., choose Swift File. Name this Store.swft. 

Add the following to this file: 

```Swift 
import Foundation
import UIKit

class Store {
	static var sharedInstance = Store()
	
	var items = [
		Item(title: "ABCD", image: UIImage(named: "robot1")!),
		Item(title: "BCDE", image: UIImage(named: "robot2")!),
		Item(title: "DEFG", image: UIImage(named: "robot3")!),
		Item(title: "EFGH", image: UIImage(named: "robot4")!)
	]
 
	var orders = [
	Order(title: "Hello", image: UIImage(named: "box")!, items: [])
	]
	
}
```

This is a very simple class that mocks up our data. 

The class is set up as a singleton. You will only ever access this class from the static property `sharedInstance`!

The class has two properties that it shares: items and orders. 

Here we have mocked up four items and one order. 

## Order History

OrderList displays a list of orders as a list or table view. 

```Swift 
struct OrdersList: View {
	var orders: [Order]
	var body: some View {
		VStack {
			List(orders) { order in
				HStack {
					Image(uiImage: order.image)
						.resizable()
						.frame(width: 50, height: 50)
					Text(order.title)
				}
			}
		}
	}
}

struct OrdersList_Previews: PreviewProvider {
	static var previews: some View {
		OrdersList(orders: Store.sharedInstance.orders)
	}
}
```

**Challenge!** Style the list. Try these ideas. Add a Spacer to separate the image and the text. 

Add some other styles. Think about the font size and style. 

Change the size of the image. Think about the space around the image, you might add some padding.

## Display a Grid of items

Modify the ProductView. You'll create a list and display the items in that list. 

```Swift 
struct ProductsView: View {
	// Declare a variable to hold items
	var items: [Item]
	// Define two columns
	let gridConfig = [
		GridItem(.fixed(80), spacing: 60),
		GridItem(.fixed(80), spacing: 60),
	]
	
	var body: some View {
	HStack {
		// Use LazyVGrid to arrange things
		LazyVGrid(columns: gridConfig) {
			// Loop over items
			ForEach(items) { item in
				// For each item make a stack
				VStack {
					Image(uiImage: item.image)
						.resizable()
						.frame(width: 80, height: 80)
					
					Spacer()
					Text(item.title)
					}
					.frame(height: 100)
					.padding(20)
					.border(.black)
				}
			}
		}
	}
}

struct ProductsView_Previews: PreviewProvider {
	static var previews: some View {
		// Populate with data from the Store
		ProductsView(items: Store.sharedInstance.items)
 	}
}
```

**Challenge!** Each element in the grid is arranged in the VStack in the ForEach loop above. This would be better as a separate view. 

Your challenge is to create a new SwiftUI View and move these elements into that view. Declare a var in that view to hold the item. 

Last use that new view inside the ForEach loop to replace what is there. 

## Navigation

Add some navigation. The orders list should allow us to display a list of items in the order. 

Add a NavigationStack to the OrdersList. 

```Swift
struct OrdersList: View {
	var orders: [Order]
	var body: some View {
	NavigationStack {
		VStack {
			List(orders) { order in
				OrderCell(order: order)
			}
			}
		}
	}
}
```

If you created a SwiftUI view for the order cell your code might look like this. 

## Adding the OrderCell 

The order cell displays the order. 

```Swift 
struct OrderCell: View {
	var order: Order
	var body: some View {
	HStack {
		Image(uiImage: order.image)
			.resizable()
			.frame(width: 50, height: 50)
		Spacer()
		Text(order.title)
		}
	}
}


struct OrderCell_Previews: PreviewProvider {
	static var previews: some View {
		OrderCell(order: Order(title: "Test", image: UIImage(named: "box")!, items: []))
	}
}
```

The order cell might look like this. Notice I mocked up the order that was provided to the view. 

Add some navigation. To start set up some navigation that will load another copy of the OrderList. 

```Swift
struct OrderCell: View {
	var order: Order
	var body: some View {
		NavigationLink(destination: OrdersList(orders: Store.sharedInstance.orders)) {
			HStack {
				Image(uiImage: order.image)
				.resizable()
				.frame(width: 50, height: 50)
				Spacer()
				Text(order.title)
			}
		}
	}
}
```

Notice that the destination of the NavigationStack is the OrderList and you are providing the list of orders from the store. 

## Make OrderDetails View

This Step will be all challenges! 

**Challenge!** Make a new SwiftUI view to show order details. 

Setup mock-up data with this view. It should display a list of items. 

**Challenge!** Use a NavigationLink and set the destination to your OrderDetails view. 

Be sure to pass the items array from the Order to the OrderDetails view. 

