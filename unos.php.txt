<!-- connect.php: -->

<?php
    $serverName='localhost';
    $userName='root';
    $password='';
    $dbName='skola';
    $conn = mysqli_connect($serverName, $userName, $password, $dbName);  
    if($conn==FALSE) echo 'Neuspješno uspostavljanje veze!';
    else echo "Konekcija sa bazom uspostavljena";
?>


<!-- prikaz.php: -->

<html>

<head>
    <title>Prikaz podataka</title>
</head>

<body>
    <?php
    require "connect.php";
    $upit='SELECT * FROM ucenici';
    $result= mysqli_query($conn,$upit);
    if (mysqli_num_rows($result)>0)
    {
    while ($row=mysqli_fetch_array($result)) 
    {
        echo $row['ime']. " ". $row['prezime']. " ". $row['broj_telefona'];
?>
    <img src="data:image/jpg;charset=utf8;base64, <?php echo $row['slike'];?> " width="100"><br><br>
    <?php
}}
	else{
	echo ("Ne postoje podaci u bazi");
	}?>
</body>

</html>


<!-- brisanje.php: -->

<html>

<body>
    <?php
require "connect.php";
$upit='SELECT * FROM ucenici';
$result= mysqli_query($conn,$upit);
if (mysqli_num_rows($result)>0)
{ ?>
    <table border="1">
        <tr>
            <td>Slika</td>
            <td>Ime</td>
            <td>Prezime</td>
            <td>Broj Telefona</td>
            <td>Obriši</td>
        </tr>
        <?php
	while ($row=mysqli_fetch_array($result))
{
	?><tr>
            <td><img src="data:image/jpg;charset=utf8;base64, <?php echo $row['slike'];?> " width="100"></td>
            <td><?php echo $row['ime'];?> </td>
            <td><?php echo $row['prezime'];?> </td>
            <td><?php echo $row[' broj_telefona '];?> </td>
            <td><a href="delete.php?remove_id=<?php echo $row['id'];?> ">Obriši</a></td>
        </tr>
        <?php
}    
	} 
else{
	echo ("Ne postoje podaci u bazi");
	}
?>
    </table>
</body>

</html>




<!-- delete.php : -->

<?php
require "connect.php";
if (isset($_GET['remove_id'])){
	$id=$_GET['remove_id'];
	$obrisi="DELETE FROM ucenici WHERE id='{$id}'";
	mysqli_query($conn,$obrisi);
	header('Location:brisanje.php');
}
?>

<!-- unos.php: -->
<html>

<head>
    <title>Unos podataka</title>
</head>

<body>
    <form action="insert.php" method="post">
        Ime:<input type="text" name="ime"><br><br>
        Prezime:<input type="text" name="prezime"><br><br>
        Broj telefona:<input type="text" name="telefon"><br><br>
        <input type="submit" name="potvrdi" value="Snimi"><br><br>
    </form>
</body>

</html>

<!-- insert.php: -->
<?php
require 'connect.php';
if(isset($_POST['potvrdi'])) {
	$Ime=$_POST['ime'];
	$Prezime=$_POST['prezime'];
	$Telefon=$_POST['telefon'];
	$Ime=mysqli_real_escape_string($conn,$Ime);
	$Prezime=mysqli_real_escape_string($conn,$Prezime);
	$Telefon=mysqli_real_escape_string($conn,$Telefon);
if (!empty($Ime) && !empty($Prezime) && !empty($Telefon))
{
$upit="INSERT INTO ucenici (ime, prezime, broj_telefona ) VALUES ('{$Ime}','{$Prezime }','{$Telefon}')";
	mysqli_query($conn,$upit);
	echo 'Podaci uspješno uneseni!';}
else {
echo 'Podaci nisu uneseni, sva polja moraju biti popunjena!';
}}?>


<!-- pretraga.php: -->

<html>

<body>
    <form action="pretraga.php" method="post">
        <input type="text" name=" trazena_vrijednost " placeholder="Унесите име и презиме..."><br><br>
        <input type="submit" name="pretrazi" value="Pretrazi">
    </form>
    <?php
require 'search';
if (mysqli_num_rows($search_result)>0){?>
    <table border="1">
        <thead>
            <tr>
                <th>Slika</th>
                <th>Ime</th>
                <th>Prezime</th>
                <th>Broj Telefona</th>
            </tr>
        </thead>
        <?php while($row = mysqli_fetch_array($search_result)){?>
        <tbody>
            <tr align="center">
                <td width="300"><img src="data:image/jpg;charset=utf8;base64, <?php echo $row['slike'];?> " width="100">
                </td>
                <td width="300"><?php echo $row['ime'];?> </td>
                <td width="300"><?php echo $row['prezime'];?> </td>
                <td width="300"><?php echo $row[' broj_telefona '];?> </td>
            </tr>
        </tbody>
        <?php
	}}
	else{
	echo ("Ne postoji takvo lice u bazi!");
	}?>
    </table>
</body>

</html>


<!-- search.php: -->

<?php
  if(isset($_POST['pretrazi']))
{
    $trazena_vrijednost = $_POST[' trazena_vrijednost '];
    $upit = "SELECT * FROM ucenici WHERE CONCAT(ime , prezime, broj_telefona) LIKE '%".$trazena_vrijednost."%'";
    $search_result = filterTable($upit);
    }
 else {
$upit = "SELECT * FROM ucenici";
}
function filterTable($upit) 
{ 													
include 'connect.php';
$filter_Result = mysqli_query($conn, $upit);
 return $filter_Result;
}
?>
