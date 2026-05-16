---
title: 利用RadixUI的Overlay解决方案
description: ""
date: 2025-02-05T08:59:05.298Z
preview: ""
draft: false
tags: [shadcn, radix, react]
categories: [web dev]
image: ""
slug: use-radix-to-do-overlay
---

shadcn-ui中有一个Dialog组件可以很快速地创建Dialog + Overlay。
其基本功能就是会显示一个黑色覆盖层，并且覆盖层中间会弹出一个窗口作为Dialog。

但是shadcn-ui中的组件已经经过充分定制。
有些时候自己已经设计好了一份弹窗，不想要shadcn的predefined style。
一种方法是去修改shadcn源码，另一种方法是直接用shadcn底层的radix来写，客制化程度更高。

这里我们用一个例子，假设我们已经有了一个Component叫做`MemberCard`，我们想要：
- 使用一个占满全屏的Overlay
- 当click到卡片外部的区域时，关闭这张`MemberCard`
- 拥有Radix的各种功能，例如无障碍，Esc退出等等。

```js
import * as Dialog from '@radix-ui/react-dialog';
import { motion } from 'framer-motion';
import { MemberInfo } from '@/types/MemberInfo';
import MemberCard from './MemberCard';
import { VisuallyHidden } from '@radix-ui/react-visually-hidden';

interface CardDialogProps {
  isOpen: boolean;
  onClose: () => void;
  member: MemberInfo;
}

const CardDialog: React.FC<CardDialogProps> = ({ isOpen, onClose, member }) => {
  return (
    <Dialog.Root open={isOpen} onOpenChange={onClose}>
      {/* Portal负责把这个组件送出dom，通常在body的下方。
      这样它就不会受到事件冒泡和父元素CSS（例如z-index）等的影响。 */ }
      <Dialog.Portal>
        {/* 透明的Overlay，记得设置覆盖全屏。 */ }
        <Dialog.Overlay className="fixed inset-0 bg-black/50 backdrop-blur-sm z-50"/>
        {/* 为了将Dialog Content居中，这里需要一个div来做flex styling。它的大小也是充满全屏的。 */ }
        <div className='fixed inset-0 z-50 flex items-center justify-center'>
          {/* 将组件包裹在这里。注意Content要和组件一样大。
          这样才能触发onPointerDownOutside，达到点击背景退出的效果。 */ }
          <Dialog.Content className="w-3/4 max-w-7xl outline-none" onPointerDownOutside={onClose}>
            {/* Radix的Dialog必须存在Title来触发无障碍。这里我们用VisuallyHidden隐藏。 */ }
            <Dialog.Title asChild>
              <VisuallyHidden>
                {member.name} personal information
              </VisuallyHidden>
            </Dialog.Title>
            
            {/* 卡片容器 */}
            <motion.div
              initial={{ scale: 0.3 }}
              animate={{ scale: 1 }}
            >
              <MemberCard {...member} />
            </motion.div>
          </Dialog.Content>
        </div>
      </Dialog.Portal>
    </Dialog.Root>
  );
};

export default CardDialog;
```

然后就可以快乐解决了！

另外，虽然这里用了workaround，
但是其实非常多的东西都可以用shadcn-ui解决，感觉它覆盖了95%以上的日常开发需求。