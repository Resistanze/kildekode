<?php
include 'core.php';
startpage();
?>
<div id="center">
    <h1 class="headline">Nyheter <?php if($user->userstatus < 4){ echo '<a href="nyheter.php?page=createnew">Legg til nyhet</a>';} ?></h1>
    <?php
    if(!isset($_GET['page'])){
?>
<table class="table" align="center">
<?php
echo date("d-m-y H:i:s");
?>
<?php
        $sql = $db->query("SELECT * FROM news ORDER BY id DESC") or die(mysqli_error());
        if($sql->num_rows == 0){
            echo '<tr><td class="info"><i>Ingen nyheter er lagt til enda!</i></td></tr>';
        }
        while($fetch = $sql->fetch_object()){
            
        $title = htmlspecialchars($fetch->title, ENT_QUOTES, "utf-8");
        $author = htmlspecialchars($fetch->author, ENT_QUOTES, "utf-8");
        $content = htmlspecialchars($fetch->content, ENT_QUOTES, "utf-8");
        $content = str_replace("\n","<br />",$content);
        $time = htmlspecialchars($fetch->time_created, ENT_QUOTES, "utf-8");
?>
        <tr>
            <th class="h"><?php echo $title . ' | Skrevet av: <a href="profil.php?nick='.$author.'">'. $author . '</a> | Dato:'.$time; ?></th>
        </tr>
        <tr>
            <td class="info"><?php echo $content; ?></td>
        </tr>
<?php
}
?>
    </table>
<?php
    }else{
        $page = $db->escape_string($_GET['page']);
        if($page == 'createnew'){
            if($user->userstatus < 4){
               
            if(isset($_POST['submit_new'])){
                $author = $db->escape_string($user->username);
                $title = $db->escape_string($_POST['new_title']);
                $content = $db->escape_string($_POST['new_content']);
                $time = date("d-m-y H:i:s");
                $db->query("INSERT INTO news (title, author, content, time_created) VALUES ('$title','$author', '$content', '$time')");
                header("Location: nyheter.php");
                
            }
?>
    <form method="POST">
    <table class="table" align="center">
        <tr>
            <th class="h" colspan="2">Legg til nyhet</th>
        </tr>
        <tr>
        <td class="info">Tittel:</td><td class="info"><input type="text" class="input_text" name="new_title" /></td>
        </tr>
        <tr>
            <td class="info">Innhold:</td><td class="info"><textarea style="width: 300px; height: 180px;" name="new_content" class="input_text"></textarea></td>
        </tr>
        <tr>
            <td class="info" colspan="2"><input type="submit" class="knapp" value="Legg til" name="submit_new" /></td>
        </tr>
    </table>
    </form>
<?php
        }else{
            echo '<p class="feil">Ingen tilgang!</p>';
        }
        }
    }
    ?>
</div>
<?php
endpage();
?>
