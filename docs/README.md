<script>
    !function(t,e){var o,n,p,r;e.__SV||(window.posthog=e,e._i=[],e.init=function(i,s,a){function g(t,e){var o=e.split(".");2==o.length&&(t=t[o[0]],e=o[1]),t[e]=function(){t.push([e].concat(Array.prototype.slice.call(arguments,0)))}}(p=t.createElement("script")).type="text/javascript",p.async=!0,p.src=s.api_host+"/static/array.js",(r=t.getElementsByTagName("script")[0]).parentNode.insertBefore(p,r);var u=e;for(void 0!==a?u=e[a]=[]:a="posthog",u.people=u.people||[],u.toString=function(t){var e="posthog";return"posthog"!==a&&(e+="."+a),t||(e+=" (stub)"),e},u.people.toString=function(){return u.toString(1)+".people (stub)"},o="capture identify alias people.set people.set_once set_config register register_once unregister opt_out_capturing has_opted_out_capturing opt_in_capturing reset isFeatureEnabled onFeatureFlags".split(" "),n=0;n<o.length;n++)g(u,o[n]);e._i.push([i,s,a])},e.__SV=1)}(document,window.posthog||[]);
    posthog.init('phc_t6IKMTGgoeESfJbos0qyYEgc757xXe9RY3p47nn2aS',{api_host:'https://eu.posthog.com'})
</script>
<script src="https://unpkg.com/vis-timeline@latest/standalone/umd/vis-timeline-graph2d.min.js"></script>
<link href="https://unpkg.com/vis-timeline@latest/styles/vis-timeline-graph2d.min.css" rel="stylesheet"
    type="text/css" />
<style>
    .vis-item .vis-item-overflow {
        overflow: visible;
    }
</style>
<script>
    function renderVisualisation(data) {
        var lines = data.split("\n");
        var items = [];
        for (var i = 0; i < lines.length; i++) {
            var parts = lines[i].split(" ");
            var regex = /\[(\d+),(\d+)\]/;
            var start = parseInt(regex.exec(parts[3])[1]);
            var end = parseInt(regex.exec(parts[3])[2]);
            var item = {
                id: i,
                content: parts[0] + " " + parts[1] + " " + parts[2],
                group: parts[0] === "INVOKE" ? parts[1] : parts[1].split("/")[3],
                start,
                end
            };
            items.push(item);
        }
        var min = Math.min.apply(null, items.map(i => i.start));
        var max = Math.max.apply(null, items.map(i => i.end));
        items.forEach(i => {
            i.start -= min;
            i.end -= min;
        });

        var groups = [];
        var groupNames = items.map(i => i.group).filter((v, i, a) => a.indexOf(v) === i);
        for (var i = 0; i < groupNames.length; i++) {
            groups.push({
                id: i,
                content: groupNames[i]
            });
        }
        // console.table(groups)
        for (var i = 0; i < items.length; i++) {
            items[i].group = groupNames.indexOf(items[i].group);
        }

        // console.table(items)
        var timeline = new vis.Timeline(
            document.getElementById('visualization'),
            new vis.DataSet(items),
            new vis.DataSet(groups), {
                format: {
                    minorLabels: {
                        millisecond: 'SSS',
                        second: 's',
                        minute: 'HH:mm:ss',
                        hour: 'HH:mm:ss',
                        weekday: 'HH:mm:ss',
                        day: 'HH:mm:ss',
                        week: 'HH:mm:ss',
                        month: 'HH:mm:ss',
                        year: 'HH:mm:ss'
                    },
                    majorLabels: {
                        millisecond: 'HH:mm:ss',
                        second: 'HH:mm:ss',
                        minute: '',
                        hour: '',
                        weekday: '',
                        day: '',
                        week: '',
                        month: '',
                        year: ''
                    }
                },
            }
        );
    }
</script>

<textarea id="data" oninput="renderVisualisation(this.value)" rows="10" cols="200"></textarea>
<div id="visualization"></div>
