# TableWithSearchBar
Swift example of Tableview with search bar

From https://www.ioscreator.com/tutorials/add-search-table-view-ios-tutorial

First open Xcode and create a new Single View App.


Remove the View Controller from the Storyboard and drag a **Navigation Controller** to the empty canvas. 
When the initial View Controller is deleted there isn't a starting point defined. Select the Navigation Controller and go to
the Attribute Inspector. In the View Controller Section  select the "Is Initial View Controller" checkbox.

 Add a new file to the project, select iOS->Source->Cocoa Touch Class. Name it TableViewController and make it a subclass of UITableViewController.
 
 Go to the TableViewController.swift file and change the class declaration line:
 ```
 class TableViewController: UITableViewController, UISearchResultsUpdating {
 ```
 
 The tableData property will hold the items of the Table View, where the filteredTableData property will contain the result from the search query. The Search Controller manages the results of the search. 
 Change the viewDidLoad method to:
 ```
 override func viewDidLoad() {
    super.viewDidLoad()

    resultSearchController = ({
        let controller = UISearchController(searchResultsController: nil)
        controller.searchResultsUpdater = self
        controller.dimsBackgroundDuringPresentation = false
        controller.searchBar.sizeToFit()

        tableView.tableHeaderView = controller.searchBar

        return controller
    })()
 ```
 A closure is used which is assigned to the resultSearchController. The results of the search will be presented in the 
 current Table View, so the searchResultsController parameter of the UISearchController init method is set to nil. Also, 
 the searchResultsUpdater property is set to the current Table View Controller and the background dimming is set to false. 
 The searchable is added to the Table View and the data is reloaded. 
 Next, implement the Table View Data Source delegate methods:
 ```
 override func numberOfSections(in tableView: UITableView) -> Int {
   // 1 
   // return the number of sections
   return 1
}

override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  // 2
  // return the number of rows
  if  (resultSearchController.isActive) {
      return filteredTableData.count
  } else {
      return tableData.count
  }
}
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  // 3
  let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)

  if (resultSearchController.isActive) {
      cell.textLabel?.text = filteredTableData[indexPath.row]

      return cell
  }
  else {
      cell.textLabel?.text = tableData[indexPath.row]
      print(tableData[indexPath.row])
      return cell
  }
}
 ```
 Only One section is being used

If the Search Controller is active the number of filtered items are assigned to the number of rows in the Table View, 
otherwise the tableData array is being used.
If the Search Controller is active, the filtered items are displayed, otherwise the original items of the tableData array are displayed.
Finally, implement the updateSearchResults(for:_) delegate method of the UISearchResultsUpdating protocol
```
func updateSearchResults(for searchController: UISearchController) {
    filteredTableData.removeAll(keepingCapacity: false)

    let searchPredicate = NSPredicate(format: "SELF CONTAINS[c] %@", searchController.searchBar.text!)
    let array = (tableData as NSArray).filtered(using: searchPredicate)
    filteredTableData = array as! [String]

    self.tableView.reloadData()
}
```

The updateSearchResults(for:_) method is called when the user updates the Search bar with input. In this method we will handle the search filtering of our search term. NSPredicate is an sort of expression that handles the search criteria. "c" means case-sensitive. The results are then assigned to the filteredTableData array and the Table View is reloaded.
Build and Run the project, enter a search query and the results are being displayed.
