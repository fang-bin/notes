1. 纯css导航栏下划线跟随效果

        <ul>
            <li>不可思议的CSS</li>
            <li>导航栏</li>
            <li>光标小下划线跟随</li>
            <li>PURE CSS</li>
            <li>Nav Underline</li>
        </ul>

        li::before {
            content: "";
            position: absolute;
            top: 0;
            left: 100%;
            width: 0;
            height: 100%;
            border-bottom: 2px solid #000;
            transition: 0.2s all linear;
        }
        li:hover::before {
            width: 100%;
            left: 0;
        }
        li:hover ~ li::before {
            left: 0;
        }
