---
title: C# Mini Project - Library Management System program detailed overview
categories: [C#, sql server]
tags: [C#, databases, windows forms, visual studio]
---

In this mini <a href="https://sbozich.github.io/posts/mini-project-car-rental/" target="_blank">project</a>, we will be creating a program in C# which serves as a Library Management System, enabling the basic functions of one such system. In particular, there are separate records of a books, books’ categories, authors, publishers, library members, as well as the records on book issues and returns. It is in fact a Windows Forms GUI program connected to the underlying database in MS SQL Server which uses similar logic as the <a href="https://sbozich.github.io/posts/mini-project-car-rental/" target="_blank">Car Rental</a> program.

![](https://github.com/sbozich/Library-Management-System/raw/main/Showcase.gif) 

## Database overview
 
The underlying database consists of 7 tables, as follows:
*	author contains data on authors of the books,
*	book contains books' records,
*	category holds the books categories,
*	issuebook records the issuance of the books,
*	member contains the library’s members data,
*	publisher saves the information on publishers,
*	returnbook records the return of the previously issued books.

## Structure of the program


![](https://sbozich.github.io/assets/22112202a.jpg)
 
The program holds a number of .cs files, each for the previously created database tables, respectively. As usual, startup Program.cs runs Main.cs which is the main selection menu of the program.
 
### Category.cs, Author.cs, Publisher.cs, Book.cs, Member.cs

![](https://sbozich.github.io/assets/22112203.jpg)
![](https://sbozich.github.io/assets/22112204.jpg)
![](https://sbozich.github.io/assets/22112205.jpg)
![](https://sbozich.github.io/assets/22112206.jpg)
![](https://sbozich.github.io/assets/22112207.jpg)

Those classes are similar to each other, in fact, in terms of both GUI elements and underlying code they consist of the same elements. In particular, they have in common DataGridView, ComboBox, TextBox elements and corresponding buttons for adding/updating the data. On the coding side, they all connect to the database, displaying its contents in aforementioned Windows Forms elements. The database can be updated as well. The database connectivity is ensured through the following code:

```C#
SqlConnection con = new SqlConnection("Data source=DESKTOP-N5IF2SJ\\SQLEXPRESS; Initial Catalog=slibrary; Integrated Security=true;");
```

As in the Car Rental example, the classes itself are consisting of multiple methods which eliminate the need for multiple-code usage. In particular, those methods are load(), getid(string id). Additionally, there are two click events which define the action on user’s button clicks. Once is one class created with such a structure and tested, creating other classes is a matter of a work in Visual Studio and repeating the same code within the other classes, paying respect to their particular contents, GUI and database elements.

Connection string:

```C#
SqlConnection con = new SqlConnection("Data source=DESKTOP-N5IF2SJ\\SQLEXPRESS; Initial Catalog=slibrary; Integrated Security=true;");
```

load() method:

```C#
public void load()
        {
            sql = "select * from category";
            cmd = new SqlCommand(sql, con);
            con.Open();

            dr = cmd.ExecuteReader();
            dataGridView1.Rows.Clear();

            while(dr.Read())
            {
                dataGridView1.Rows.Add(dr[0], dr[1], dr[2]);
            }

            con.Close();
        }
```

getid(string id) method:

```C#
public void getid(string id)
        {
            sql = "select * from category where id= '" + id + "'";
            cmd = new SqlCommand(sql, con);
            con.Open();
            dr = cmd.ExecuteReader();
            while(dr.Read())
            {
                txtname.Text = dr[1].ToString();
                txtstatus.Text = dr[2].ToString();

            }
            con.Close();
        }
```

button1 click event:

```C#
private void button1_Click(object sender, EventArgs e)
        {
            string catname = txtname.Text;
            string status = txtstatus.SelectedItem.ToString();
            id = dataGridView1.CurrentRow.Cells[0].Value.ToString();

            if (Mode==true)
            {
                sql = "insert into category(catname,status) values(@catname, @status)";
                con.Open();
                cmd = new SqlCommand(sql, con);
                cmd.Parameters.AddWithValue("@catname", catname);
                cmd.Parameters.AddWithValue("@status", status);
                cmd.ExecuteNonQuery();
                MessageBox.Show("Category created");
                txtname.Clear();
                //txtstatus.Items.Clear();
                txtstatus.SelectedIndex = -1;
                txtname.Focus();
            }

            else
            {
                sql = "update category set catname=@catname, status=@status where id=@id";
                con.Open();
                cmd = new SqlCommand(sql, con);
                cmd.Parameters.AddWithValue("@catname", catname);
                cmd.Parameters.AddWithValue("@status", status);
                cmd.Parameters.AddWithValue("@id", id);
                cmd.ExecuteNonQuery();
                MessageBox.Show("Category updated");
                txtname.Clear();
                //txtstatus.Items.Clear();
                txtstatus.SelectedIndex = -1;
                txtname.Focus();
            }
        }
```


datagridview click event:

```C#
private void dataGridView1_CellContentClick(object sender, DataGridViewCellEventArgs e)
        {
            if(e.ColumnIndex==dataGridView1.Columns["edit"].Index && e.RowIndex>=0)
            {
                Mode = false;
                id = dataGridView1.CurrentRow.Cells[0].Value.ToString();
                getid(id);

            }
            else if (e.ColumnIndex == dataGridView1.Columns["delete"].Index && e.RowIndex >= 0)
            {
                Mode = false;
                id = dataGridView1.CurrentRow.Cells[0].Value.ToString();

                sql = "delete from category where id=@id";
                con.Open();
                cmd = new SqlCommand(sql, con);
                
                cmd.Parameters.AddWithValue("@id", id);
                cmd.ExecuteNonQuery();
                MessageBox.Show("Category deleted");
                txtname.Clear();
                //txtstatus.Items.Clear();
                txtstatus.SelectedIndex = -1;
                txtname.Focus();
                con.Close();
            }
        }

```

### LendBook.cs, ReturnBook.cs

![](https://sbozich.github.io/assets/22112208.jpg)
![](https://sbozich.github.io/assets/22112209.jpg)

The class lendbook.cs is similar to previous, the new elements are issuing and return date elements. Nothing is computed here but passed to the database. The calculation of the days is done in the returnbook.cs class: similar to the Car Rental project, the calculation is done in SQL and passed back to the program:

```C#
cmd = new SqlCommand("select book,issuedate,returndate,DATEDIFF(dd,returndate,GETDATE())as elap from issuebook where memberid='"+ txtmid.Text+ "'",con);
```

## Conclusion
The presented program takes into consideration records of authors, books, books categories, members and publishers, and records the books' issuing and return date. Upon return, it calculates the number of passed days, and if it greater than allowed, calculates the fine. 
