# kk-player




将加入以下wp模板函数functions.php


从这里开始插入
function get_lrc($lrc_url){   
    if( $lrc_url ){   
        // 远程获取歌词内容   
        $content = @file_get_contents($lrc_url);   
           
        // 按”回车换行“将歌词切割成数组   
        $array = explode("\n", $content);
		
        $lrc = array();   
  
        foreach($array as $val){   
            // 清除掉”回车不换行“符号   
            $val = preg_replace('/\r/', '', $val);   
               
            // 正则匹配歌词时间   
            $temp = preg_match_all('/\[\d{2}\:\d{2}\.\d{2}\]/', $val, $matches); 
			
            if( !empty($matches[0]) ){   
                $data_plus = "";   
                $time_array = array();   
                   
                // 将可能匹配的多个时间挑选出来，例如：[00:21]、[03:40]   
                foreach($matches[0] as $V){   
                    $data_plus .= $V;   
                    $V = str_replace("[", "", $V);   
                    $V = str_replace("]", "", $V);   
                    $date_array = explode(":", $V);   
                    
                    // 将例如：00:21、03:40 转换成秒   
                    $time_array[] = $date_array[0]*60 + $date_array[1];//intval();   
                }   
  
                // 将上面的得到的时间，例如：[00:21][03:40]，替换成空，得到歌词   
                $data_plus = str_replace($data_plus, "", $val);   
                   
                // 将时间和歌词组合到数组中   
                foreach($time_array as $V){   
                    $lrc[] = array($V, $data_plus);   
                }
            }   
        }    
        return $lrc;   
    }   
    return false;   
}   

function music($atts,$content = null){
	extract(shortcode_atts(array('lrc' => '',), $atts));
	$url = get_template_directory_uri().'/player/';
	echo '<link rel="stylesheet" type="text/css" href="'.$url.'/kkplayer.min.css" />'."\n";
	echo '<script type="text/javascript" src="'.$url.'/kkplayer.min.js" async defer></script>'."\n";
    echo "<KK-Player src=\"".$content."\">\n";
	$lrc = get_lrc($lrc);
	foreach($lrc as $V){
	 echo '<Lyric Time="'.$V[0].'">'.$V[1]."</Lyric> \n";
	}
	echo '</KK-Player>';
}
add_shortcode('music' , 'music');
?>

这里是结尾



调用方式   使用短代码调用
[music lrc="1.lrc"]1.mp3[/music]   
