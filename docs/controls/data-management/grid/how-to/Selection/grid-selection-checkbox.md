---
title: Make Selection with Checkbox Column
page_title:  Make Selection with Checkbox Column | Kendo UI Grid
description: "Learn how to select a Kendo UI Grid row with a checkbox column, preserve the selection and get the IDs of all selected items."
slug: howto_make_selection_checkbox_column_grid
---

# Make Selection with Checkbox Column

As of the Kendo UI R2 2017 SP1 release, the [selection of columns with checkboxes](http://docs.telerik.com/kendo-ui/api/javascript/ui/grid#configuration-columns.selectable) is a built-in feature for
the Grid.

For earlier versions, refer to the following example. It demonstrates how to select a Grid row by using a checkbox, preserve the selection between pages, and get the IDs of the selected items from all pages.

###### Example

```html
<div id="grid"></div>
<button id="showSelection">Show selected IDs</button>
<script>
    $(document).ready(function () {
        //DataSource definition
        var crudServiceBaseUrl = "https://demos.telerik.com/kendo-ui/service",
        dataSource = new kendo.data.DataSource({
            transport: {
                read: {
                    url: crudServiceBaseUrl + "/Products",
                    dataType: "jsonp"
                },
                update: {
                    url: crudServiceBaseUrl + "/Products/Update",
                    dataType: "jsonp"
                },
                destroy: {
                    url: crudServiceBaseUrl + "/Products/Destroy",
                    dataType: "jsonp"
                },
                create: {
                    url: crudServiceBaseUrl + "/Products/Create",
                    dataType: "jsonp"
                },
                parameterMap: function (options, operation) {
                    if (operation !== "read" && options.models) {
                        return {
                            models: kendo.stringify(options.models)
                        };
                    }
                }
            },
            batch: true,
            pageSize: 20,
            schema: {
                model: {
                    id: "ProductID",
                    fields: {
                        ProductID: {
                            editable: false,
                            nullable: true
                        },
                        ProductName: {
                            validation: {
                                required: true
                            }
                        },
                        UnitPrice: {
                            type: "number",
                            validation: {
                                required: true,
                                min: 1
                            }
                        },
                        Discontinued: {
                            type: "boolean"
                        },
                        UnitsInStock: {
                            type: "number",
                            validation: {
                                min: 0,
                                required: true
                            }
                        }
                    }
                }
            }
        });

        //Grid definition
        var grid = $("#grid").kendoGrid({
            dataSource: dataSource,
            pageable: true,
            height: 430,
            //define dataBound event handler
            dataBound: onDataBound,
            toolbar: ["create"],
            columns: [
            //define template column with checkbox and attach click event handler
            { template: "<input type='checkbox' class='checkbox' />" },
            "ProductName", {
                field: "UnitPrice",
                title: "Unit Price",
                format: "{0:c}",
                width: "100px"
                }, {
                field: "UnitsInStock",
                title: "Units In Stock",
                width: "100px"
                }, {
                field: "Discontinued",
                width: "100px"
                }, {
                command: ["edit", "destroy"],
                title: "&nbsp;",
                width: "172px"
            }
            ],
            editable: "inline"
        }).data("kendoGrid");

        //bind click event to the checkbox
        grid.table.on("click", ".checkbox" , selectRow);

        $("#showSelection").bind("click", function () {
            var checked = [];
            for(var i in checkedIds){
                if(checkedIds[i]){
                    checked.push(i);
                }
            }

            alert(checked);
        });
    });

    var checkedIds = {};

    //on click of the checkbox:
    function selectRow() {
        var checked = this.checked,
        row = $(this).closest("tr"),
        grid = $("#grid").data("kendoGrid"),
        dataItem = grid.dataItem(row);

        checkedIds[dataItem.id] = checked;
        if (checked) {
            //-select the row
            row.addClass("k-state-selected");
            } else {
            //-remove selection
            row.removeClass("k-state-selected");
        }
    }

    //on dataBound event restore previous selected rows:
    function onDataBound(e) {
        var view = this.dataSource.view();
        for(var i = 0; i < view.length;i++){
            if(checkedIds[view[i].id]){
                this.tbody.find("tr[data-uid='" + view[i].uid + "']")
                .addClass("k-state-selected")
                .find(".checkbox")
                .attr("checked","checked");
            }
        }
    }
</script>
```

## See Also

* [JavaScript API Reference of the Grid](/api/javascript/ui/grid)
* [How to Persist Row Selection while Paging, Sorting, and Filtering]({% slug howto_persist_row_selection_paging_sorting_filtering_grid %})
* [How to Prevent Selection for Checkbox Cells]({% slug howto_prevent_selection_checkbox_cells_grid %})
* [How to Select Multiple Rows with Checkboxes]({% slug howto_select_multiple_rowswith_checkboxes_grid %})

For more runnable examples on the Kendo UI Grid, browse its [**How To** documentation folder]({% slug howto_adjust_row_heights_template_locked_columns_grid %}).
