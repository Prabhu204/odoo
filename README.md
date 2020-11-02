# Odoo V12 development tasks

Installation process:

From the Odoo github repository, clone the Odoo 12.0 version source code by using:

```
git clone https://github.com/odoo/odoo.git

```

Please make that you have PostgresDB installed and before running Odoo in the local system.

However, I have observed that the docker version Odoo do not let you modify the source code. That's because
it does not have any ssh configured file in it.

Once everything is setup  go to the project root folder. There you will
see a file named `odoo-bin` and then run the following command in the terminal 

```
odoo-bin --dev=reload --xmlrpc-port=8070
```

Well, I have changed the default port for the project since my 8069 port is used by other application.
If everything is working then you will see a page like this under the url `0.0.0.0:8069` or `0.0.0.0:8070`. It depends
on the port that application is using. 

![Alt text](images_readme/indexpage.jpg?raw=true "Index page")

Create a database and do not forget to click the Demo data option. It will automatically download a sample data for your
application. And then select create a database.

After successful login, you will see a bunch apps menu and go head select sales app and it will install that app for you.
Also, you will notice a list of products and along with some properties init.   


In order to modify the field of a product or menu display in kanban view you need to activate debug mode from the settings section.

Once you activate it, you will a following icon in your navigation bar.

image


Source code modification can be done either by web gui or at the backend(in Pycharm IDE). However, if you modify any xml code 
through web GUI, those changes are temporary. If you update all apps you will loose those changes. Hence it is recommended
modify in the through backend. Further you have the flexibility to develop your own custom module or 
to integrate new functionalities your Odoo application. These changes are permanent.  

### 1. Module 1 - "Product Category in Kanban View"

To display product category in the kanban view of a product list navigate the following path:

`addons/product/views/product_views.xml` add the following line of code under product name field in kanban section in xml.

```
<field name="categ_id" string="Product Category"/>
```

To see your changes in GUI, update the sales app in settings. 

### 2. Module 2 - Extended Product State

To add additional apps(i.e third party apps), download the required file and unzip it. And add that folder in addons folder.
Then refresh your apps list, you will see that added file moreover, it will show a state header bar in product view.

### 3. Assigning color to product state buttons

Since state has a fixed default color and a flexibility of adding more states, it is rather difficult to assign color to 
each ste with specific color. Hence, I have approached with assigning buttons as per state. Each button assigned specific color for 
state change.

### 4. Displaying warning message upon Obsolete state selection
   
It is rather depends on the database model that has the product quantity field. Since default model do not 
have such filed, I have added the following line of code in the product template model to crete new field in the database.

```
available_quantity = fields.Integer('Available quantity', required=True, readonly=False)

```

And then I have updated the list again.

Then goal of this section is to avoid the products which are more than one product to obsolete state. To achieve this functionality
and to show a warning message I have added the a new function in the following file at the location:
`addons/product_state/models/product_template.py`

```
# This function is triggered when the user clicks on the button 'Done'
    @api.one
    def obsolete_progressbar(self):
        self.write({
            'state': 'obsolete',
        })
        current_id = self.ids
        product_quantity = self.env['product.template'].browse(current_id)
        for rec in product_quantity:
            print(f"Product id {rec.id}\tProduct name {rec.name}\t product quantity {rec.available_quantity}")
            if rec.available_quantity > 0:
                raise ValidationError("State Obsolete not possible, Product has stock amount >0")
  
```
 
For educational purposes I experimented with the [original source code](https://github.com/odoo/odoo/tree/12.0). I thank 
all contributors of this open source project.